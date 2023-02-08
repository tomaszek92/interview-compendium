# Architecture
![image](/assets/angular/achitecture.png)
## Module
Modules are logical boundaries in your application and the application is divided into separate modules to separate the functionality of your application. Lets take an example of `app.module.ts` root module declared with `@NgModule` decorator as below,

```typescript
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent }  from './app.component';

@NgModule ({
   imports:      [ BrowserModule ],
   declarations: [ AppComponent ],
   bootstrap:    [ AppComponent ],
   providers: []
})
export class AppModule { }
```
The NgModule decorator has five important (among all) options:
- The imports option is used to import other dependent modules. The BrowserModule is required by default for any web based angular application.
- The declarations option is used to define components in the respective module.
- The bootstrap option tells Angular which Component to bootstrap in the application.
- The providers option is used to configure a set of injectable objects that are available in the injector of this module.
- The entryComponents option is a set of components dynamically loaded into the view.

## Directive
Directives add behaviour to an existing DOM element or an existing component instance.

### ngFor
We use Angular ngFor directive in the template to display each item in the list. For example, here we iterate over list of users,
```html
<li *ngFor="let user of users">
  {{ user }}
</li>
```
#### trackBy
The main purpose of using `*ngFor` with `trackBy` option is performance optimization. Normally if you use `*ngFor` with large data sets, a small change to one item by removing or adding an item, can trigger a cascade of DOM manipulations. In this case, Angular sees only a fresh list of new object references and to replace the old DOM elements with all new DOM elements. You can help Angular to track which items added or removed by providing a `trackBy` function which takes the index and the current item as arguments and needs to return the unique identifier for this item.

### ngIf
Sometimes an app needs to display a view or a portion of a view only under specific circumstances. The Angular ngIf directive inserts or removes an element based on a truthy/falsy condition. Let's take an example to display a message if the user age is more than 18,
```html
<p *ngIf="user.age > 18">You are not eligible for student pass!</p>
```
Angular isn't showing and hiding the message. It is adding and removing the paragraph element from the DOM. That improves performance, especially in the larger projects with many data bindings.

### ngSwitch
NgSwitch directive is similar to JavaScript switch statement which displays one element from among several possible elements, based on a switch condition. In this case only the selected element placed into the DOM. It has been used along with `*ngSwitch`, `*ngSwitchCase` and `*ngSwitchDefault` directives.

For example, let's display the browser details based on selected browser using ngSwitch directive.

```html
<div [ngSwitch]="currentBrowser.name">
  <chrome-browser *ngSwitchCase="'chrome'" [item]="currentBrowser"></chrome-browser>
  <firefox-browser *ngSwitchCase="'firefox'" [item]="currentBrowser"></firefox-browser>
  <opera-browser *ngSwitchCase="'opera'" [item]="currentBrowser"></opera-browser>
  <safari-browser *ngSwitchCase="'safari'" [item]="currentBrowser"></safari-browser>
  <ie-browser *ngSwitchDefault [item]="currentItem"></ie-browser>
</div>
```

## Component
Components are the most basic UI building block of an Angular app, which form a tree of Angular components. These components are a subset of directives. Unlike directives, components always have a template, and only one component can be instantiated per element in a template.

## Template
A template is a HTML view where you can display data by binding controls to properties of an Angular component. You can store your component's template in one of two places. You can define it inline using the template property, or you can define the template in a separate HTML file and link to it in the component metadata using the @Component decorator's templateUrl property.

## Service
When you have data or logic that has to be shared across components, a service class is created. The class is always associated with the @Injectible decorator.

## Directive vs Component?
Write a component when you want to create a reusable set of DOM elements of UI with custom behaviour. Write a directive when you want to write reusable behaviour to supplement existing DOM elements.

## Metadata
Metadata is used to decorate a class so that it can configure the expected behavior of the class. The metadata is represented by decorators.
- **Class decorators**, e.g. `@Component` and `@NgModule`
- **Property decorators** used for properties inside classes, e.g. `@Input` and `@Output`
- **Method decorators** used for methods inside classes, e.g. `@HostListener`
- **Parameter decorators** used for parameters inside class constructors, e.g. `@Inject`, `@Optional`

## Pipe
A pipe takes in data as input and transforms it to a desired output. For example, let us take a pipe to transform a component's birthday property into a human-friendly date using date pipe.
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({name: 'customFileSizePipe'})
export class FileSizePipe implements PipeTransform {
    transform(size: number, extension: string = 'MB'): string {
        return (size / (1024 * 1024)).toFixed(2) + extension;
    }
}
...
template: `
      <h2>Find the size of a file</h2>
      <p>Size: {{288966 | customFileSizePipe: 'GB'}}</p>
    `
```

### What is the difference between pure and impure pipe?
A pure pipe is only called when Angular detects a change in the value or the parameters passed to a pipe. For example, any changes to a primitive input value (String, Number, Boolean, Symbol) or a changed object reference (Date, Array, Function, Object). An impure pipe is called for every change detection cycle no matter whether the value or parameters changes. i.e, An impure pipe is called often, as often as every keystroke or mouse-move.

## Dependency injection
Dependency injection (DI) is an important application design pattern in which a class asks for dependencies from external sources rather than creating them itself. Angular comes with its own dependency injection framework for resolving dependencies( services or objects that a class needs to perform its function).

# Lifecycle
Angular application goes through an entire set of processes or has a lifecycle right from its initiation to the end of the application.

![image](/assets/angular/lifecycle.png)

- `ngOnChanges`
    - Respond when Angular sets or resets data-bound input properties. The method receives a `SimpleChanges` object of current and previous property values.
    - Called before `ngOnInit` (if the component has bound inputs) and whenever one or more data-bound input properties change.
    - This happens frequently, so any operation you perform here impacts performance significantly.
- `ngOnInit`
    - Initialize the directive or component after Angular first displays the data-bound properties and sets the directive or component's input properties.
    - Called once, after the first `ngOnChanges`. `ngOnInit` is still called even when `ngOnChanges` is not (which is the case when there are no template-bound inputs).
- `ngDoCheck`
    - Detect and act upon changes that Angular can't or won't detect on its own.
    - Called immediately after `ngOnChanges` on every change detection run, and immediately after `ngOnInit` on the first run.
- `ngAfterContentInit`
    - Respond after Angular projects external content into the component's view, or into the view that a directive is in.
    - Called once after the first `ngDoCheck`.
- `ngAfterContentChecked`
    - Respond after Angular checks the content projected into the directive or component.
    - Called after `ngAfterContentInit` and every subsequent `ngDoCheck`.
- `ngAfterViewInit`
    - Respond after Angular initializes the component's views and child views, or the view that contains the directive.
    - Called once after the first `ngAfterContentChecked`.
- `ngAfterViewChecked`
    - Respond after Angular checks the component's views and child views, or the view that contains the directive.
    - Called after the `ngAfterViewInit` and every subsequent `ngAfterContentChecked`.
- `ngOnDestroy`
    - Cleanup just before Angular destroys the directive or component. Unsubscribe Observables and detach event handlers to avoid memory leaks. 
    - Called immediately before Angular destroys the directive or component.

## What is the difference between constructor and ngOnInit?
The `constructor` is a default method of the class that is executed when the class is instantiated and ensures proper initialisation of fields in the class and its subclasses. Angular, or better Dependency Injector (DI), analyses the constructor parameters and when it creates a new instance it tries to find providers that match the types of the constructor parameters, resolves them and passes them to the constructor.

The `ngOnInit` is a life cycle hook called by Angular to indicate that Angular is done creating the component.
Mostly we use ngOnInit for all the initialization/declaration and avoid stuff to work in the constructor. The constructor should only be used to initialize class members but shouldn't do actual "work". 

So you should use constructor() to setup Dependency Injection and not much else. `ngOnInit` is better place to e.g. http call.

# Data binding
Data binding is a core concept in Angular and allows to define communication between a component and the DOM, making it very easy to define interactive applications without worrying about pushing and pulling data.
## From the Component to the DOM
### Interpolation `{{ value }}`
Adds the value of a property from the component
```html
<li>Name: {{ user.name }}</li>
<li>Address: {{ user.address }}</li>
```
### Property binding `[property]=”value”`
The value is passed from the component to the specified property or simple HTML attribute
```html
<input type="email" [value]="user.email">
```
## From the DOM to the Component (event binding `(event)=”function”`)
When a specific DOM event happens (eg.: click, change, keyup), call the specified method in the component
```html
<button (click)="logout()"></button>
```

##  Two-way data binding `[(ngModel)]=”value”`
Two-way data binding allows to have the data flow both ways. For example, in the below code snippet, both the email DOM input and component email property are in sync
```html
<input type="email" [(ngModel)]="user.email">
```

# Compilation
## Ahead-of-Time (AOT)
A type of compilation that compiles your app at build time. This is the default starting in Angular 9.

## Just-in-Time (JIT)
A type of compilation that compiles your app in the browser at runtime. JIT compilation was the default until Angular 8, now default is AOT.

## What are the advantages with AOT?
- Faster rendering: The browser downloads a pre-compiled version of the application. So it can render the application immediately without compiling the app.
- Fewer asynchronous requests: It inlines external HTML templates and CSS style sheets within the application javascript which eliminates separate ajax requests.
- Smaller Angular framework download size: Doesn't require downloading the Angular compiler. Hence it dramatically reduces the application payload.
- Detect template errors earlier: Detects and reports template binding errors during the build step itself
- Better security: It compiles HTML templates and components into JavaScript. So there won't be any injection attacks.

## Why do we need compilation process?
The Angular components and templates cannot be understood by the browser directly. Due to that Angular applications require a compilation process before they can run in a browser. For example, In AOT compilation, both Angular HTML and TypeScript code converted into efficient JavaScript code during the build phase before browser runs it.

# Reactive forms
Reactive forms is a model-driven approach for creating forms in a reactive style. These are built around observable streams, where form inputs and values are provided as streams of input values.

# Change detection
## Default
By default, Angular walks the application’s component tree, an array, from start to end. As seen in the code above, the check itself is straightforward. It compares the previous value with the current (or the current with the next one) and updates the component accordingly. Since we can pass mutable objects, the change detection has to check every binding in every template every time – Angular cannot know which of the values is bound at some point. 
![image](/assets/angular/change_detection_default.png)

## OnPush
The OnPush strategy changes Angular’s change detection behavior in a similar way as detaching a component does. The change detection doesn’t run automatically for every component anymore. Angular instead listens for specific changes and only runs the change detection on a subtree for that component.
![image](/assets/angular/change_detection_on_push.png)

# HttpClient
The major advantages of HttpClient:
- Contains testability features
- Provides typed request and response objects
- Intercept request and response
- Supports Observalbe APIs
- Supports streamlined error handling

# Http Interceptors
Http Interceptors are part of `@angular/common/http`, which inspect and transform HTTP requests from your application to the server and vice-versa on HTTP responses. These interceptors can perform a variety of implicit tasks, from authentication to logging.

The syntax of HttpInterceptor interface looks like as below,
```typescript
interface HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>
}
```
You can use interceptors by declaring a service class that implements the `intercept()` method of the HttpInterceptor interface.
```typescript
@Injectable()
export class MyInterceptor implements HttpInterceptor {
    constructor() {}
    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        ...
    }
}
```
After that you can use it in your module,
```typescript
@NgModule({
    ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MyInterceptor,
            multi: true
        }
    ]
    ...
})
export class AppModule {}
```

# What is host property in css?
The `:host` pseudo-class selector is used to target styles in the element that hosts the component. Since the host element is in a parent component's template, you can't reach the host element from inside the component by other means. For example, you can create a border for parent element as below,

```scss
:host {
  display: block;
  border: 1px solid black;
  padding: 20px;
}
```

# Lazy loading
Lazy loading is one of the most useful concepts of Angular Routing. It helps us to download the web pages in chunks instead of downloading everything in a big bundle. It is used for lazy loading by asynchronously loading the feature module for routing whenever required using the property loadChildren.
```typescript
const routes: Routes = [
  {
    path: 'customers',
    loadChildren: () => import('./customers/customers.module').then(module => module.CustomersModule)
  },
  {
    path: 'orders',
    loadChildren: () => import('./orders/orders.module').then(module => module.OrdersModule)
  },
  {
    path: '',
    redirectTo: '',
    pathMatch: 'full'
  }
];
```

# What are the security principles in angular?
- Use the latest Angular library releases.
- Don't modify your copy of Angular.
- Avoid Angular APIs marked in the documentation as “Security Risk.”
- You should avoid direct use of the DOM APIs.
- You should enable Content Security Policy (CSP) and configure your web server to return appropriate CSP HTTP headers.
- You should use the offline template compiler.
- You should use Server Side XSS protection.
- You should use DOM Sanitizer.
- You should preventing CSRF or XSRF attacks.

# RxJS
RxJS is a library for composing asynchronous and callback-based code in a functional, reactive style using Observables. Many APIs such as HttpClient produce and consume RxJS Observables and also uses operators for processing observables.

## What is the difference between promise and observable?
| Observable | Promise |
| ---------- | ------- |
| Declarative: Computation does not start until subscription so that they can be run whenever you need the result | Execute immediately on creation |
| Provide multiple values over time | Provide only one |
| Subscribe method is used for error handling which makes centralized and predictable error handling | Push errors to the child promises |
| Provides chaining and subscription to handle complex applications | Uses only .then() clause |

## Observables
- **They are cold:** Code gets executed when they have at least a single observer.
- **Creates copy of data**: Observable creates copy of data for each observer.
- **Uni-directional**: Observer can not assign value to observable.

## Subject
- **They are hot**: code gets executed and value gets broadcast even if there is no observer.
- **Shares data**: Same data get shared between all observers.
- **bi-directional**: Observer can assign value to observable.

If you are using subject then you miss all the values that are broadcast before creation of observer. So here comes `ReplaySubject`.

## ReplaySubject
- **They are hot**: code gets executed and value get broadcast even if there is no observer.
- **Shares data**: Same data get shared between all observers.
- **bi-directional**: Observer can assign value to observable.
- **Replay the message stream**: No matter when you subscribe the replay subject you will receive all the broadcasted messages.

In `Subject` and `ReplaySubject`, you cannot set the initial value to observable. So here comes `BehavioralSubject`.

## BehaviorSubject
- **They are hot**: code gets executed and value get broadcast even if there is no observer.
- **Shares data**: Same data get shared between all observers.
- **bi-directional**: Observer can assign value to observable.
- **You can set initial value**: You can initialize the observable with a default value.

## switchMap
Projects each source value to an Observable which is merged in the output Observable, emitting values only from the most recently projected Observable.

`switchMap` in RxJS only cares about the latest value that the observable emitted, in this case cancelling any previous HTTP requests that were still in progress.

A good use case for `switchMap` would be an autocomplete input, where we should discard all but the latest results from the user’s input.

![image](/assets/angular/switchMap.png)


## mergeMap
Projects each source value to an Observable which is merged in the output Observable.

`mergeMap` in RxJS passes all requests through, even when a new request was made before a previous one had finished.

![image](/assets/angular/mergeMap.png)

## concatMap
Projects each source value to an Observable which is merged in the output Observable, in a serialized fashion waiting for each one to complete before merging the next.

![image](/assets/angular/concatMap.png)
