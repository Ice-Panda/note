# Unix 环境高级编程

# 基础知识

# 文件 I/O

## Linux 中的文件

Linux 中一切皆文件,物理文件、设备、管道、内存。广义就是 Linux 管理的所有对象，这些文件利用 VFS 机制，以文件系统的形式挂在在 Linux 内核中，对外提供一致的文件操作接口。

**文件描述符**是一个非负整数，本质是一个句柄，句柄对用户是透明的。用户空间利用文件描述符与内核进行交互。内核利用文件描述符管理真正的数据。

每个进程都维护一个文件表，用于维护该进程代开文件的信息，例如：打开个数，每个打开文件偏移量等。

### 内核文件的实现

```c
/*
 * Open file table structure
 */
struct files_struct {
  /*
   * read mostly part
   */
	atomic_t count;
	bool resize_in_progress;
	wait_queue_head_t resize_wait;

	struct fdtable __rcu *fdt;
	struct fdtable fdtab;
  /*
   * written part on a separate cache line in SMP
   */
	spinlock_t file_lock ____cacheline_aligned_in_smp;
	unsigned int next_fd;
	unsigned long close_on_exec_init[1];
	unsigned long open_fds_init[1];
	unsigned long full_fds_bits_init[1];
	struct file __rcu * fd_array[NR_OPEN_DEFAULT];
};
```

init 是 linux 第一个进程,他的文件表是
```c
struct files_struct init_files = {
	.count		= ATOMIC_INIT(1),
	.fdt		= &init_files.fdtab,
	.fdtab		= {
		.max_fds	= NR_OPEN_DEFAULT,
		.fd		= &init_files.fd_array[0],
		.close_on_exec	= init_files.close_on_exec_init,
		.open_fds	= init_files.open_fds_init,
		.full_fds_bits	= init_files.full_fds_bits_init,
	},
	.file_lock	= __SPIN_LOCK_UNLOCKED(init_files.file_lock),
};
```

## 打开文件

`int open(const char *pathname, int flags, mode_t mode);`

open接收可变参数,glibc声明是 `extern int open(__const char *__file, int __oflag, ...) __nonnull((1));`

- pathname: 要打开的文件路径
- flags: O_RDONLY==0、O_WRONLY==1和O_RDWR==2
- mode: 只在创建文件事需要,用于指定所创建文件的权限为(收到 umask 影响)

### 更多选项

- O_APPEND: 每次写操作都会追加到文件末尾
- O_ASYNC: 使用异步 I/O 模式
- O_CLOEXEC: 在打开文件的时候，就为文描述符设置 FD_CLOEXEC 标志，这是一个新的选项，用于解决在多线程下fork与用fcntl设置FD_CLOEXEC的竞争问题。在多线程下，可能会在fcntl调用前，就已经fork出子进程了，从而导致该文件句柄暴露给子进 程。
- O_CREAT: 当文件不存在时，就创建文件
- O_DIRECT:对该文件进行直接I/O，不使用VFS Cache。
- O_DIRECTORY: 要求打开的路径必须是目录。
- O_EXCL:该标志用于确保是此次调用创建的文件，需要与O_CREAT同时使用;当文件已经存在 时，open函数会返回失败
- O_LARGEFILE:表明文件为大文件
- O_NOATIME:读取文件时，不更新文件最后的访问时间。
- O_NONBLOCK、O_NDELAY:将该文件描述符设置为非阻塞的(默认都是阻塞的)
- O_SYNC:设置为I/O同步模式，每次进行写操作时都会将数据同步到磁盘，然后write才能返回。
- O_TRUNC:在打开文件的时候，将文件长度截断为0，需要与O_RDWR或O_WRONLY同时使用。

> 不一定所有文件系统都支持上面选项

### open 源码跟踪

open->do_sys_open

```c
long do_sys_open(int dfd, const char __user *filename, int flags, umode_t mode)
{
	struct open_flags op;
    /* flags为用户层传递的参数，内核会对flags进行合法性检查，
    并根据mode生成新的flags值赋给fd */
	int fd = build_open_flags(flags, mode, &op);
	struct filename *tmp;

	if (fd)
		return fd;//没有出错返回文件句柄


	tmp = getname(filename);
	if (IS_ERR(tmp))
		return PTR_ERR(tmp);
    
	fd = get_unused_fd_flags(flags);
	if (fd >= 0) {
        // 申请新的文件管理结构
		struct file *f = do_filp_open(dfd, tmp, &op);
		if (IS_ERR(f)) {
			put_unused_fd(fd);
			fd = PTR_ERR(f);
		} else {
            // 将文件描述符fd与文件管理结构file对应起来，即安装
			fsnotify_open(f);
			fd_install(fd, f);
		}
	}
	putname(tmp);
	return fd;
}
```
do_sys_open可以看出，打开文件时，内核主要消耗：文件描述符与内核管理文件结构 file

### 如何选择文件描述符

根据POSIX标准，当获取一个新的文件描述符时，要返回最低的未使用的文件描述符.

`do_sys_open->get_unused_fd_flags->alloc_fd(0，(flags))`

```C
static int alloc_fd(unsigned start, unsigned flags)
{
	return __alloc_fd(current->files, start, rlimit(RLIMIT_NOFILE), flags);
}
/*
 * allocate a file descriptor, mark it busy.
 */
int __alloc_fd(struct files_struct *files,
	       unsigned start, unsigned end, unsigned flags)
{
	unsigned int fd;
	int error;
	struct fdtable *fdt;
    // files为进程的文件表，下面需要更改文件表，所以需要先锁文件表
	spin_lock(&files->file_lock);
repeat:
    // 得到文件描述符表
	fdt = files_fdtable(files);
	fd = start;
    // 从start开始，查找未用的文件描述符。在打开文件时
    // files->next_fd为上一次成功找到的fd的下一个描述符。
    // 使用next_fd，可以快速找到未用的文件描述符;
	if (fd < files->next_fd)
		fd = files->next_fd;

    // 当小于当前文件表支持的最大文件描述符个数时，利用位图找到未用的文件描述符。
    // 如果大于max_fds怎么办呢?如果大于当前支持的最大文件描述符，那它肯定是未用的，就不需要用位图来确认了。
	if (fd < fdt->max_fds)
		fd = find_next_fd(fdt, fd);

	/*
	 * N.B. For clone tasks sharing a files structure, this test
	 * will limit the total number of files that can be opened.
	 */
	error = -EMFILE;
	if (fd >= end)
		goto out;
    // expand_files用于在必要时扩展文件表。
    // 何时是必要的时候呢?比如当前文件描述符已经超过了当前文件表支持的最大值的时候。
	error = expand_files(files, fd);
	if (error < 0)
		goto out;

	/*
	 * If we needed to expand the fs array we
	 * might have blocked - try again.
	 */
	if (error)
		goto repeat;

	if (start <= files->next_fd)
		files->next_fd = fd + 1;

	__set_open_fd(fd, fdt);
	if (flags & O_CLOEXEC)
		__set_close_on_exec(fd, fdt);
	else
		__clear_close_on_exec(fd, fdt);
	error = fd;
#if 1
	/* Sanity check */
	if (rcu_access_pointer(fdt->fd[fd]) != NULL) {
		printk(KERN_WARNING "alloc_fd: slot %d not NULL!\n", fd);
		rcu_assign_pointer(fdt->fd[fd], NULL);
	}
#endif

out:
	spin_unlock(&files->file_lock);
	return error;
}
```

### 文件描述符fd 与文件管理结构 file

内核使用 fd_install 将文件 file 与 fd 组合起来

```c
void fd_install(unsigned int fd, struct file *file)
{
	__fd_install(current->files, fd, file);
}
void __fd_install(struct files_struct *files, unsigned int fd,
		struct file *file)
{
	struct fdtable *fdt;

	rcu_read_lock_sched();

	if (unlikely(files->resize_in_progress)) {
		rcu_read_unlock_sched();
		spin_lock(&files->file_lock);
		fdt = files_fdtable(files);
		BUG_ON(fdt->fd[fd] != NULL);
		rcu_assign_pointer(fdt->fd[fd], file);
		spin_unlock(&files->file_lock);
		return;
	}
	/* coupled with smp_wmb() in expand_fdtable() */
	smp_rmb();
	fdt = rcu_dereference_sched(files->fdt);
	BUG_ON(fdt->fd[fd] != NULL);
	rcu_assign_pointer(fdt->fd[fd], file);
	rcu_read_unlock_sched();
}
```

当用户使用 fd 与内核交互时,内核可以使用 fd 从 fdt->fd[fd]中得到内部管理文件的结构 struct_file

## create

create就是`open(pathname，O_WRONLY|O_CREAT|O_TRUNC，mode)`

## 关闭文件

close 用于关闭文件描述符.文件描述符可以普通文件、设备、socket等。在关闭时，VFS 会根据不同的文件类型，执行不同的操作。

### close 源码跟踪

```c
/*
 * The same warnings as for __alloc_fd()/__fd_install() apply here...
 */
int __close_fd(struct files_struct *files, unsigned fd)
{
    //files当前进程的文件表

	struct file *file;
	struct fdtable *fdt;

	spin_lock(&files->file_lock);
    // 通过文件表，取得文件描述符表
	fdt = files_fdtable(files);
    //参数fd大于文件描述符表记录的最大描述符，那么它一定是非法的描述符
	if (fd >= fdt->max_fds)
		goto out_unlock;
    //利用fd作为索引，得到file结构指针
	file = fdt->fd[fd];
    // 检查filp是否为NULL。正常情况下，filp一定不为NULL。
	if (!file)
		goto out_unlock;
    // 清除fd在close_on_exec位图中的位
	rcu_assign_pointer(fdt->fd[fd], NULL);
    // 释放该fd，或者说将其置为unused。
	__put_unused_fd(files, fd);
	spin_unlock(&files->file_lock);
    // 关闭file结构
	return filp_close(file, files);

out_unlock:
	spin_unlock(&files->file_lock);
	return -EBADF;
}

static void __put_unused_fd(struct files_struct *files, unsigned int fd)
{
	struct fdtable *fdt = files_fdtable(files);
	__clear_open_fd(fd, fdt);
	if (fd < files->next_fd)
		files->next_fd = fd;
}
```
Linux 选择文件描述符是从小到大的顺序进行寻找的,文件表中 next_fd 用于记录下一次开始寻找的起点,当有空闲描述符时,即可分配.
当某个文件描述符关闭时,如果其小鱼 next_fd, 则 next_fd 就重置为这个描述符,这样下一次就会立即重用这个描述符。
**linux 永远会选择最小的可用的文件描述符**

最终使用的时文件系统的 release 函数,从而实现针对不同的文件类型来执行不同的关闭操作


## 自定义 files_operations

linux中不同文件类型使用的操作函数定义在files_operations中,例如 socket 文件操作函数:

```c
static const struct file_operations socket_file_ops = {
	.owner =	THIS_MODULE,
	.llseek =	no_llseek,
	.read_iter =	sock_read_iter,
	.write_iter =	sock_write_iter,
	.poll =		sock_poll,
	.unlocked_ioctl = sock_ioctl,
#ifdef CONFIG_COMPAT
	.compat_ioctl = compat_sock_ioctl,
#endif
	.mmap =		sock_mmap,
	.release =	sock_close,
	.fasync =	sock_fasync,
	.sendpage =	sock_sendpage,
	.splice_write = generic_splice_sendpage,
	.splice_read =	sock_splice_read,
};
```

不同文件类型利用 `**_alloc_file`来申请文件描述符及文件管理结构 file 结构,他们最终都会调用alloc_file来申请文件管理结构 file,例如:

```c
struct file *sock_alloc_file(struct socket *sock, int flags, const char *dname)
{
	struct qstr name = { .name = "" };
	struct path path;
	struct file *file;

	if (dname) {
		name.name = dname;
		name.len = strlen(name.name);
	} else if (sock->sk) {
		name.name = sock->sk->sk_prot_creator->name;
		name.len = strlen(name.name);
	}
	path.dentry = d_alloc_pseudo(sock_mnt->mnt_sb, &name);
	if (unlikely(!path.dentry)) {
		sock_release(sock);
		return ERR_PTR(-ENOMEM);
	}
	path.mnt = mntget(sock_mnt);

	d_instantiate(path.dentry, SOCK_INODE(sock));

    // 这里把socket_file_ops赋值给了file->f_op 从而实现了 VFS 中可以调用 socket 文件系统自定义的操作
	file = alloc_file(&path, FMODE_READ | FMODE_WRITE,
		  &socket_file_ops);
	if (IS_ERR(file)) {
		/* drop dentry, keep inode for a bit */
		ihold(d_inode(path.dentry));
		path_put(&path);
		/* ... and now kill it properly */
		sock_release(sock);
		return file;
	}

	sock->file = file;
	file->f_flags = O_RDWR | (flags & O_NONBLOCK);
	file->private_data = sock;
	return file;
}
```

alloc_file

```c
struct file *alloc_file(const struct path *path, fmode_t mode,
		const struct file_operations *fop)
{
	struct file *file;

	file = get_empty_filp();
	if (IS_ERR(file))
		return file;

	file->f_path = *path;
	file->f_inode = path->dentry->d_inode;
	file->f_mapping = path->dentry->d_inode->i_mapping;
	file->f_wb_err = filemap_sample_wb_err(file->f_mapping);
	if ((mode & FMODE_READ) &&
	     likely(fop->read || fop->read_iter))
		mode |= FMODE_CAN_READ;
	if ((mode & FMODE_WRITE) &&
	     likely(fop->write || fop->write_iter))
		mode |= FMODE_CAN_WRITE;
	file->f_mode = mode;
	file->f_op = fop;
	if ((mode & (FMODE_READ | FMODE_WRITE)) == FMODE_READ)
		i_readcount_inc(path->dentry->d_inode);
	return file;
}
```

### 遗忘 close 造成的问题

- 文件描述符始终没有释放
- 文件管理的内存结构没有释放

当进程退出时, linux ,会关闭进程的文件释放内存,当进程不死,这些内存就一直在那边.

当再次申请文件描述符时,就会扩展当前文件表,如果打开超过了系统限制就会发生**Too many open files**错误

### 如何朝赵文件资源泄露


