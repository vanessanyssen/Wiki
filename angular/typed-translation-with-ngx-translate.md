[Home](../README.md) /

# Typed Translation with @Ngx-Translate

_**Sources:**_

- [Angular + @ngx-translate + Typings (Carlos Caballero)](https://www.carloscaballero.io/angular-ngx-translate-typings/)
- [Injecting Context through Structural Directives in Angular (Felipe Gonçalves Marques)](https://medium.com/taqtilebr/injecting-context-through-structural-directives-in-angular-218ff35645c7)
- [Typing the directive's context (angular.io)](https://angular.io/guide/structural-directives#typing-the-directives-context)

---

## TL;DR

Going from:

```html
<h1>{{ 'HOME.HELLO' | translate: param }}!</h1>
```

to:

```html
<h1>{{ t.HOME.HELLO | translate: param }}!</h1>
```

where `t` is a **_Typed object_** representing the translation key-values.

It can be provided through the constructor of the component :

```typescript
  constructor(public t: T9nService) {}
```

or with a structural directive that'll inject the service as context directly into the view :

```html
<ng-container *t9n="let t">
  <h1>{{ t.HOME.HELLO | translate: param }}!</h1>
</ng-container>
```

Use TS Object for you translations :

```typescript
/* src/i18n/en.ts */
const en = {
  //...
};
export default en;

/* src/i18n/fr.ts */
const fr: typeof import("./en").default = {
  //...
};
export default fr;
```

This will help the IDE to detect all discrepancies between the translation files like mising key or typo. It will also provide auto-completion while adding a translation in the template.

_**Pros**_

- We have autocomplete in IDE, as for any other nested TS Object.
- Strong typing: we can know directly if a key
  - is incomplete or has typo ;
  - is missing from one language file to another.
- We can change the name of a key automatically (refactoring) between all the languages by simply renaming the symbol.
- Migration is easy as the translate pipe is still working with the previous key-string.
- Interpolated params still works.
- Duplicate values for translation: we can add a reference to another key with getter and `this` (not-so-nice solution) or define a `common` object in the translation files with all common values, and refer to it.
- We can also use functions in our translations, for complicated cases, or dynamic value, etc.
- We don't have to worry anymore with the 'cached' json when updating the translations, as we don't have json anymore, but TS files and webpack loader (\*)

_**Cons**_

- We have to import the translation service into each page that needs translations, so we can use it...
  - but we can also inject it directly in the template with a structural directive.
- Migration : we have to update all the translation key-strings in our templates to use the translations service...
  - but it can be done progressively, as it's still working with the old key-strings.

(\*) _About cache_

> When we use Webpack Dynamic Imports, we don't have to worry anymore about cache and cache-busting.
> Each translation file is lazy loaded when user change the language, no cache is used.
> Webpack creates a separate chunk for each one of the translation files. When we run `ng build --configuration production`, all files are named using a hash, based on the file's content checksum.
> As long as the content of the file isn't changed, we'll always get the same hash.

---

## The problem

The translations are based on a key-value translation object, ususally from a json file

```json
{
  "HOME": {
    "HELLO": "Welcome to {{value}}",
    "LINKS": {
      "content": "Here are some links to help you start:",
      "first": "Tour of Heroes",
      "second": "CLI documentation",
      "third": "Angular blog"
    }
  }
}
```

Sometimes, this can also be found in this format :

```json
{
  "HOME.HELLO": "Welcome to {{value}}",
  "HOME.LINKS.content": "Here are some links to help you start:",
  "HOME.LINKS.first": "Tour of Heroes",
  "HOME.LINKS.second": "CLI documentation",
  "HOME.LINKS.third": "Angular blog"
}
```

To identify the key that we want to translate we must specify a string. This causes us to lose all the type control of the variables that we want to translate.

We do not have autocomplete, despite having nested objects that can be complex. So we must be very carefull and check in the translation file if the path is correct : if we made a typo, or missed a nested key, our IDE won't be able to warn us.

Also, if we add a new translation, and forgot to add it in one file, we have no mean to know it other than deploying and checking all the pages in all the languages !

The string is hard coded in our template. If we want to change the name of a key, we must modify it everywhere: the translation files, but also all our templates. Sure, search and replace can help, but if this key is very common or exists in another nested key, we'll have a hard time (reason why some projects use the extended path format).

## The solution

### Typing the translation files

> I will not present how you can install or use [@ngx-translate](https://github.com/ngx-translate/core). Please refer to their official documentation.

We must create a TS file for each of our translations. If we have already a json file, either our IDE can handle a copy-paste while converting json to TS, or we can use one of the many tools found inline.

```typescript
const en = {
  HOME: {
    HELLO: 'Welcome to {{value}}',
    LINKS: {
      content: 'Here are some links to help you start':,
      first: 'Tour of Heroes',
      second: 'CLI documentation',
      third: 'Angular blog'
    }
  }
};
export default en;
```

We must not forget to add the Type in the other files, so our IDE can inform us (with nice little red wave) if the structure is not exactly the same (missing keys, typos, etc.).

```typescript
const fr: typeof import("./en").default = {
  //...
};
export default fr;
```

### The translation service

Now we need to create a new service that will resolve the path to the location of the specified translation key.

i.e. we want this service to returns an object like that :

```typescript
HOME: {
   HELLO: "HOME.HELLO",
   LINKS: {
     content: "HOME.LINKS.content",
     first: "HOME.LINKS.first",
     second: "HOME.LINKS.second",
     third: "HOME.LINKS.third",
   },
}
```

Thus `t.HOME.HELLO` will returns `'HOME.HELLO'`, so that @ngx-translate receives the string it expects.

For that we will use the `GenericClassFactory` function : this function provides the same properties of any generic type T to any class that extends from it :

```typescript
type GenericClass<T> = { new (): T };

export function GenericClassFactory<T>(): GenericClass<T> {
  return class {} as GenericClass<T>;
}
```

And then we will create the service extending from `GenericClasFactory` by specifying that the properties are belonging to the `en.ts` file (our base translation file). Thus all properties in this class will be the same as in all our translation files.

```typescript
import en from "src/i18n/en";

@Injectable({
  providedIn: "root",
})
export class T9nService extends GenericClass<typeof en>() {
  constructor() {
    super();
    // has all same properties as 'en' (typeof en)
    Object.assign(this, transformObjectToPath("", en));
    // add its own path as value for each (nested) properties
  }
}
```

The `transformObjectToPath` function is responsible for building an object with the keys of `en.ts` file, where the value would be the complete path of each key.

```typescript
type Dictionary<T> = {
  [x: string]: T | Dictionary<T>;
};

function concatIfExistsPath(path: string, suffix: string): string {
  return path ? `${path}.${suffix}` : suffix;
}

function transformObjectToPath<T extends string | Dictionary<string>>(
  suffix: string,
  objectToTransformOrEndOfPath: T,
  path = ""
): string | Dictionary<string> {
  return typeof objectToTransformOrEndOfPath === "object"
    ? Object.entries(objectToTransformOrEndOfPath).reduce<Dictionary<string>>(
        (objectToTransform, [key, value]) => {
          objectToTransform[key] = transformObjectToPath(
            key,
            value,
            concatIfExistsPath(path, suffix)
          );
          return objectToTransform;
        },
        {}
      )
    : concatIfExistsPath(path, suffix);
}
```

Last, we must now define our own loader for @ngx-translate, since the .json files are not going to be downloaded using the `httpClient` service but rather a charger, that will be created to allow perform the loading of TS files :

```typescript
type ESModule = { default: unknown };

class WebpackTranslateLoader implements TranslateLoader {
  getTranslation(lang: string) {
    return from(import(`./i18n/${lang}.ts`)).pipe(
      map((m: ESModule) => m.default)
    );
  }
}

@NgModule({
  declarations: [AppComponent],
  imports: [
    //...,
    TranslateModule.forRoot({
      loader: {
        provide: TranslateLoader,
        useClass: WebpackTranslateLoader,
      },
    }),
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

The loader has been called `WebpackTranslateLoader` because `Webpack` is the one in charge of analyzing the possible files that are imported with the keyword `import (...)` and packages them as independent sources in order to carry out their request dynamically.

Finally, in order to use it in our template, we mist import the service in the constructor of our component :

```typescript
  constructor(public t: T9nService) {}
```

and

```html
<h1>{{ t.HOME.HELLO | translate: param }}!</h1>
```

## Icing on the cake : inject the translation service with a structural directive

### Structural directive

In a structural directive we can inject context in order to use it in our projected template.

```html
<div *foo="let bar">{{ bar }}</div>
```

`let bar` is the same as `let bar = $implicit`. The `$implicit` variable is sugared syntax as you can omit it when connecting to a variable.

Let's create this "simple" structural directive:

```typescript
type Ctx = { $implicit: string };

@Directive({
  selector: "[foo]",
})
export class FooDirective implements OnInit {
  private context: Ctx;

  constructor(
    private templateRef: TemplateRef<Ctx>,
    // gets a reference to the template where the directive is used
    private viewContainer: ViewContainerRef // get a view container to render the template
  ) {
    this.context = { $implicit: "foo" };
  }

  ngOnInit(): void {
    this.viewContainer.createEmbeddedView(this.templateRef, this.context);
    // render the template in the view container, with it's additionnal context
  }
}
```

```html
<div *foo="let bar">{{ bar }}</div>
<!-- will render 'foo' -->
```

### Typing the context

By default, the context provided in the instantiated template by the structural directive is not typed (here, `bar` type is inferred as `any`).

You can properly type it inside the template by providing a static `ngTemplateContextGuard` function. This will help the Angular template type checker to find mistakes in the template at compile time, which can avoid runtime errors.

```typescript
@Directive({
  selector: "[foo]",
})
export class FooDirective implements OnInit {
  //...

  // add this to provide Type guard to the context
  static ngTemplateContextGuard(_dir: FooDirective, ctx: unknown): ctx is Ctx {
    return true;
  }
}
```

Use vscode extension "[Angular Language Service](https://marketplace.visualstudio.com/items?itemName=TypeScriptTeam.AngularLanguageService)". To get the most complete information in the editor, set the `strictTemplates` option in `tsconfig.json`, as shown here:

```json
"angularCompilerOptions": {
  "strictTemplates": true
}
```

### Directive for the translation service

```typescript
@Directive({
  selector: "[t9n]",
})
export class T9nDirective implements OnInit {
  constructor(
    private templateRef: TemplateRef<{ $implicit: T9nService }>,
    private viewContainer: ViewContainerRef,
    private t: T9nService
  ) {}

  static ngTemplateContextGuard(
    _dir: T9nDirective,
    ctx: unknown
  ): ctx is { $implicit: T9nService } {
    return true;
  }

  ngOnInit() {
    this.viewContainer.createEmbeddedView(this.templateRef, {
      $implicit: this.t,
    });
  }
}
```
