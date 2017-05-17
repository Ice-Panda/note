# 路由

Angular的路由器是一个可选的服务，它用来呈现指定的URL所对应的视图。 它并不是Angular核心库的一部分，而是在它自己的@angular/router包中。 像其它Angular包一样，我们可以从它导入所需的一切。

`import { RouterModule, Routes } from '@angular/router';`

## 路由配置

每个带路由的Angular应用都有一个Router（路由器）服务的单例对象。当浏览器的URL变化时，路由器会查找对应的Route（路由），并据此决定该显示哪个组件。

路由器需要先配置才会有路由信息。 下面的例子创建了四个路由定义，并用RouterModule.forRoot方法来配置路由器， 并把它的返回值添加到AppModule的imports数组中。

```javascript
// src/app/app.module.ts (excerpt)
const appRoutes: Routes = [
  { path: 'crisis-center', component: CrisisListComponent },
  { path: 'hero/:id',      component: HeroDetailComponent },
  {
    path: 'heroes',
    component: HeroListComponent,
    data: { title: 'Heroes List' }
  },
  { path: '',
    redirectTo: '/heroes',
    pathMatch: 'full'
  },
  { path: '**', component: PageNotFoundComponent }
];

@NgModule({
  imports: [
    RouterModule.forRoot(appRoutes)
  ],
})
export class AppModule { }
```

每个Route都会把一个URL的path映射到一个组件。 **注意，path不能以斜杠（/）开头。** 路由器会为解析和构建最终的URL，这样当我们在应用的多个视图之间导航时，可以任意使用相对路径和绝对路径。

第三个路由中的data属性用来存放于每个具体路由有关的任意信息。该数据可以被任何一个激活路由访问，并能用来保存诸如 页标题、面包屑以及其它静态只读数据。本章稍后的部分，我们将使用resolve守卫来获取动态数据。

第四个路由中的空路径（''）表示应用的默认路径，当URL为空时就会访问那里，因此它通常会作为起点。 这个默认路由会重定向到URL /heroes，并显示HeroesListComponent。

最后一个路由中的**路径是一个通配符。当所请求的URL不匹配前面定义的路由表中的任何路径时，路由器就会选择此路由。 这个特性可用于显示"404 - Not Found"页，或自动重定向到其它路由。

## 路由出口

有了这份配置，当本应用在浏览器中的URL变为/heroes时，路由器就会匹配到path为heroes的Route，并在宿主视图中的RouterOutlet之后显示HeroListComponent组件。 `<router-outlet></router-outlet>`

## 路由器链接

现在，我们已经有了配置好的一些路由，还找到了渲染它们的地方，但又该如何导航到它呢？固然，从浏览器的地址栏直接输入URL也能做到，但是大多数情况下，导航是某些用户操作的结果，比如点击一个A标签。

```html
<h1>Angular Router</h1>
<nav>
    <a routerLink="/crisis-center" routerLinkActive="active">Crisis Center</a>
    <a routerLink="/heroes" routerLinkActive="active">Heroes</a>
</nav>
<router-outlet></router-outlet>
```
a标签上的RouterLink指令让路由器得以控制这个a元素。 这里的导航路径是固定的，因此可以把一个字符串赋给routerLink（“一次性”绑定）。

如果需要更加动态的导航路径，那就把它绑定到一个返回链接参数数组的模板表达式。 路由器会把这个数组解析成完整的URL。

**每个a标签上的RouterLinkActive指令可以帮用户在外观上区分出当前选中的“活动”路由。 当与它关联的RouterLink被激活时，路由器会把CSS类active添加到这个元素上。 我们可以把该指令添加到a元素或它的父元素上。**

## 路由器状态

在导航时的每个生命周期成功完成时，路由器会构建出一个ActivatedRoute组成的树，它表示路由器的当前状态。 我们可以在应用中的任何地方用Router服务及其routerState属性来访问当前的RouterState值。

路由器状态为我们提供了从任意激活路由开始向上或向下遍历路由树的一种方式，以获得关于父、子、兄弟路由的信息

## 重定向路由

需要一个pathMatch属性，来告诉路由器如何用URL去匹配路由的路径，否则路由器就会报错。 在本应用中，路由器应该只有在完整的URL等于''时才选择HeroListComponent组件，因此我们要把pathMatch设置为'full'。

从技术角度说，pathMatch = 'full'导致URL中剩下的、未匹配的部分必须等于''。 在这个例子中，跳转路由在一个顶级路由中，因此剩下的URL和完整的URL是一样的。
pathMatch的另一个可能的值是'prefix'，它会告诉路由器：当剩下的URL以这个跳转路由中的prefix值开头时，就会匹配上这个跳转路由。
在这里不能这么做！如果pathMatch的值是'prefix'，那么每个URL都会匹配上''。
尝试把它设置为'prefix'，然后点击Go to sidekicks按钮。别忘了，它是一个无效URL，本应显示“Page not found”页。 但是，我们看到了“英雄列表”页。在地址栏中输入一个无效的URL，我们又被路由到了/heroes。 每一个URL，无论有效与否，都会匹配上这个路由定义。
默认路由应该只有在整个URL等于''时才重定向到HeroListComponent，别忘了把重定向路由设置为pathMatch = 'full'。

## 路由模块

```javascript
// src/app/app-routing.module.ts
import { NgModule }              from '@angular/core';
import { RouterModule, Routes }  from '@angular/router';
import { CrisisListComponent }   from './crisis-list.component';
import { HeroListComponent }     from './hero-list.component';
import { PageNotFoundComponent } from './not-found.component';
const appRoutes: Routes = [
  { path: 'crisis-center', component: CrisisListComponent },
  { path: 'heroes',        component: HeroListComponent },
  { path: '',   redirectTo: '/heroes', pathMatch: 'full' },
  { path: '**', component: PageNotFoundComponent }
];
@NgModule({
  imports: [
    RouterModule.forRoot(appRoutes)
  ],
  exports: [
    RouterModule
  ]
})
export class AppRoutingModule {}

// src/app/app.module.ts
import { NgModule }       from '@angular/core';
import { BrowserModule }  from '@angular/platform-browser';
import { FormsModule }    from '@angular/forms';
import { AppComponent }     from './app.component';
import { AppRoutingModule } from './app-routing.module';
import { CrisisListComponent }   from './crisis-list.component';
import { HeroListComponent }     from './hero-list.component';
import { PageNotFoundComponent } from './not-found.component';
@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    AppRoutingModule
  ],
  declarations: [
    AppComponent,
    HeroListComponent,
    CrisisListComponent,
    PageNotFoundComponent
  ],
  bootstrap: [ AppComponent ]
})
export class AppModule { }


```
