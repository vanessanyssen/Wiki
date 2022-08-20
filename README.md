# lessons learned

## ANGULAR & TYPESCRIPT

- [Tune up the strictness](angular/tune-up-the-strictness.md)⭐
  - Typescript `strict` flag
  - Angular `strictTemplates` flag and `extendedDiagnostics` (ng13)
  - Improve incrementally with **Betterer**
  - Use **Eslint** + **Prettier**
    > Note: TSLint has been deprecated as of 2019.
    > Typescript-Eslint is now your best option for linting TypeScript.
    > Use Prettier for formatting rules.
  - Good practices
- [Typed Translation with @Ngx-Translate](angular/typed-translation-with-ngx-translate.md) ⭐
  - Add auto-completion and strong typing to your translations in the template
  - Catch any error at compile time
- Generic types, Type-Guards and Mapped types to ease your life
- You don't have to use Enums
  - Beware of numeric enum
  - How enums are compiled in javascript
  - Replace enum in Angular ?

## ANGULAR 14

- Standalone component ⭐
  - prepare with SCAM + AIM 
- `inject` function and dependency injection functions ⭐
- Typed Forms - see Typed formBuilder

## ANGULAR STRUCTURE

- Page / Container / View + LIFT rule
  - Page components - lazy loaded (→ route data & params)
  - Container ("smart") components (→ domain/features)
  - View ("dumb") components (→ reusable ui components)
    - Opt-in/Opt-out directives (+ dynamic validator)
- CoreModule's guard for import once
- No more shared module - use Proxy module (Ng14)

## ANGULAR FORMS

- Reactive (aka Model-Driven) form
  - v.s. Template-Driven form - pros & cons
  - Typed formBuilder
  - Validators and different version of dynamic validators
- Custom formcontrol (with Control Value Accessor)
  - Works well with changeDetectionStrategy onPush
- Dynamic form errors
  - Add dynamiccaly errors to your field forms
  - 1 service to unit test !

## ANGULAR PERFORMANCE

- ChangeDetectionStrategy onPush
- Do not use method in template
- Unsubscribe
  - Use async pipe
  - Create custom subscribe directive
- `*ngFor` with `trackBy`

## ANGULAR DYNAMIC/GENERIC COMPONENTS

- dynamic/automatic navigation
  - ActiveRoute service with childrenPath
- Generic component to display data
  - Dynamic pipe
  - Generic data view-model

## ANGULAR TESTING

- Unit test with Spectator, jest and ngx-mock
- Integration & e2e tests with Cypress

## ANGULAR & RxJS

- "Light" State Management with Store
- Push v.s. Pull strategy
