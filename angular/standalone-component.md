[Home](../README.md) /

# Standalone components

_**Sources:**_

- [Angular Standalone Components: Welcome to a World Without NgModule](https://netbasal.com/angular-standalone-components-welcome-to-a-world-without-ngmodule-abd3963e89c5)
- [A guide to Standalone Components in Angular](https://blog.ninja-squad.com/2022/05/12/a-guide-to-standalone-components-in-angular/)

---

Angular 14 introduces the possibility to declare standalone components/pipes/directives, and to get rid of `NgModule`. 

For a demo, let's start a new Angular 14 project on [stackblitz](https://stackblitz.com/).

## Standalone component

Open `hello.component.ts` and declared it as `standalone`:

```typescript
@Component({
  selector: 'hello',
  template: `<h1>Hello {{name}}!</h1>`,
  styles: [`h1 { font-family: Lato; }`],
  standalone: true, // <--
})
export class HelloComponent {
  ...
}
```

You now get an error :

```
Any standalone component/directive/pipe can’t be declared in an NgModule. 
But it can be directly imported into another standalone component. 
```

In an existing application that has no standalone components, you can also import a standalone component in the imports of an `NgModule`. In `app.module.ts`:

```typescript
@NgModule({
  imports: [BrowserModule, FormsModule, HelloComponent], // <-- add here
  declarations: [
    AppComponent,
    //  HelloComponent  <-- remove here
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

Now it's working again.

## Application bootstrap

Now we will go a step further : we will turn our `app.component.ts` into a standalone component:

```typescript
@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  standalone: true,
})
export class AppComponent {
  ...
}
```

And again, we broke our App :

```
Component AppComponent is standalone, and cannot be declared in an NgModule. 
Did you mean to import it instead?
```

If `AppComponent` is now a standalone component, we don't need anymore `AppModule`. However, `main.ts` contains a call to `platformBrowserDynamic().bootstrapModule(AppModule)` which bootstraps the main Angular module of the application.

Angular now offers a new function called `bootstrapApplication()` in `@angular/platform-browser`. The function expects the root standalone component as a parameter:

```typescript
//platformBrowserDynamic().bootstrapModule(AppModule) <-- remove
bootstrapApplication(AppComponent) // <-- replace by
 ```

What if our `AppModule` was importing another `NgModule` as `HttpClientModule`, for example ?

The `bootstrapApplication()` can take a list of providers that should be available to the root component and all its children:

```typescript

bootstrapApplication(AppComponent, {
    providers: [importProvidersFrom(HttpClientModule)]
}) 
 ```

After deleting `app.module.ts`, we got a new error...

```
'hello' is not a known element:
1. If 'hello' is an Angular component, then verify that it is included in the
 '@Component.imports' of this component.
2. To allow any element add 'NO_ERRORS_SCHEMA' to the '@Component.schemas' of 
this component.
```

Of course, we previously imported `HelloComponent` into `AppModule`. As both `HelloComponent` and `AppComponent` are now standalone, we can import the first one into the other.

```typescript
@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
  standalone: true,
  imports: [HelloComponent] // <-- hello, there :-)
})
export class AppComponent {
  ...
}
```
And now our App is working again :-D

→ The `imports` property specifies the template dependencies of the component — the directives, components, and pipes it can use. Standalone components can import other standalone components, directives, pipes, and existing `NgModules`.

## Usage in existing applications

We are not obliged to use standalone components, pipes and directives. But what good can they offer ?

Angular applications tend to have a `SharedModule` with commonly used components, directives, and pipes. You can take these and convert them to a standalone version. It's usually straightforward, as they have few dependencies. 

And then, instead of importing the full `SharedModule` in every `NgModule`, you can import just what you need!

(to be continued...)