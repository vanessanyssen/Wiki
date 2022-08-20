[Home](../README.md) /

# Tune up the strictness

_**Sources:**_

- [Be proactive when you join an Angular project](https://timdeschryver.dev/blog/be-proactive-when-you-join-an-angular-project#tune-up-the-strictness)
- [Why to and How To Use Strict Mode in Angular Applications](https://betterprogramming.pub/why-to-and-how-to-use-strict-mode-in-angular-applications-1e6f6ffc0595)
- [Bulletproof Angular. Angular strict mode explained](https://indepth.dev/posts/1402/bulletproof-angular)
- [Bulletproof TypeScript — code quality rules beyond strict mode](https://medium.com/generic-ui/bulletproof-typescript-code-quality-rules-beyond-strict-mode-315e48611a88)
- [Extended Diagnostics](https://blog.angular.io/angular-extended-diagnostics-53e2fa19ece9)
  
---

## Why strict mode ?

- Helps the compiler to infer the type of something and thus prevents from doing mistakes.
- Turns runtime errors into compile-time errors, so they never reach production.
- Diminishes the risk of changing something unintended while refactoring.
- Increases productivity, as there'll be no need anymore to analyze the complete code flow to be sure of what the type is or whether it could be null or undefined.
- With Angular template strict options, the generated code from the HTML templates of the components can be checked to match the input type requirements. And for example, it will allow to use [Typed Translation with @Ngx-Translate](typed-translation-with-ngx-translate.md).

## Enable strict mode in Typescript

The strict mode can be enabled by setting the `compilerOptions.strict` property to `true` in the `tsconfig.json` file.

`.strict` flag is a superset containing multiple strict options. Each option can be enabled separately :

- [`alwaysStrict`](https://www.typescriptlang.org/tsconfig#alwaysStrict)
- [`strictNullChecks`](https://www.typescriptlang.org/tsconfig#strictNullChecks)
- [`strictBindCallApply`](https://www.typescriptlang.org/tsconfig#strictBindCallApply)
- [`strictFunctionTypes`](https://www.typescriptlang.org/tsconfig#strictFunctionTypes)
- [`strictPropertyInitialization`](https://www.typescriptlang.org/tsconfig#strictPropertyInitialization)
- [`noImplicitAny`](https://www.typescriptlang.org/tsconfig#noImplicitAny)
- [`noImplicitThis`](https://www.typescriptlang.org/tsconfig#noImplicitThis)
- [`useUnknownInCatchVariable`](https://www.typescriptlang.org/tsconfig#useUnknownInCatchVariables)

Besides the `strict` flag, you can also enable more properties that result in a safer environment :

- Inheritance 
  - [`noImplicitOverride`](https://www.typescriptlang.org/tsconfig#noImplicitOverride)

- Index signatures
  - [`noPropertyAccessFromIndexSignature`](https://www.typescriptlang.org/tsconfig#noPropertyAccessFromIndexSignature)

- Critical path
  - [`noImplicitReturns`](https://www.typescriptlang.org/tsconfig#noImplicitReturns)
  - [`noFallthroughCasesInSwitch`](https://www.typescriptlang.org/tsconfig#noFallthroughCasesInSwitch)

**These are now enabled by default in a new Angular 13 project**.

Other flags for Type Checking:

- Dead code
  - [noUnusedLocals](https://www.typescriptlang.org/tsconfig#noUnusedLocals)
  - [noUnusedParameters](https://www.typescriptlang.org/tsconfig#noUnusedParameters)
  - [allowUnreachableCode](https://www.typescriptlang.org/tsconfig#allowUnreachableCode)
  - [allowUnusedLabels](https://www.typescriptlang.org/tsconfig#allowUnusedLabels)

- Index signatures
  - [noUncheckedIndexedAccess](https://www.typescriptlang.org/tsconfig#noUncheckedIndexedAccess)
  - [exactOptionalPropertyTypes](https://www.typescriptlang.org/tsconfig#exactOptionalPropertyTypes)

## Angular strict mode

### strictTemplate

Angular also provides a `strictTemplates` option. This option can be compared to the Typescript `strict` option, but for the HTML templates.

The `strictTemplates` option can be enabled by setting the `angularCompilerOptions.strictTemplates` to `true` in the `tsconfig.json` file.

### Extended diagnostics

Extended diagnostics were released in Angular v13.2.0. These diagnostics give compile-time warnings with precise, actionable suggestions for the templates.

Currently, Angular supports the following extended diagnostics:

- [NG8101 - invalidBananaInBox](https://angular.io/extended-diagnostics/NG8101)
- [NG8102 - nullishCoalescingNotNullable](https://angular.io/extended-diagnostics/NG8102)

## Improve incrementally with **Betterer**

The first time one or both strict options are enabled in an already existing project, there will probably be some (or lots of) errors when trying to run (and build) the application. These need to be addressed first, before the application can run again.

Turning on `strictPropertyInitialization`, especially, will change the way you are writing your code. Check out [Why to and How To Use Strict Mode in Angular Applications](https://betterprogramming.pub/why-to-and-how-to-use-strict-mode-in-angular-applications-1e6f6ffc0595) for more informations.

Refactoring all the errors at once is the best scenario, but when there are too many of them, **[Betterer](https://github.com/phenomnomnominal/betterer)** provides a solution to incrementally improve the state of the codebase.

By using **Betterer**, all the errors don't need to be fixed in one go, and the development process can continue. **Betterer** allows to take care of the errors one by one, without the addition of new errors.

**Betterer** provides those built-in tests :

- [TypeScript test](https://phenomnomnominal.github.io/betterer/docs/typescript-test)
- [Angular test](https://phenomnomnominal.github.io/betterer/docs/angular-test)

## Use Eslint

In the early years Angular relied on TSLint to statically analyze the code. Because TSLint was included with the creation of a new Angular project, a lot of older Angular projects still depend on TSLint. 

 In 2019-2020, TSLint became deprecated and was ported over to ESLint as [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint).
 
For Angular projects, there's the [angular-eslint](https://github.com/angular-eslint/angular-eslint) ESLint plugin, which is the ESLint equivalent of `codelyzer`.

Other usefull plugins:
- [eslint-plugin-rxjs](https://github.com/cartant/eslint-plugin-rxjs)
- [eslint-plugin-rxjs-angular](https://github.com/cartant/eslint-plugin-rxjs-angular)

💡 Here again, **Betterer** can help if there are too many errors to fix at once by using the built-in [ESLint Test](https://phenomnomnominal.github.io/betterer/docs/eslint-test/).

## Good practices
- Update Angular (but check your team's policy before 😅)
- Stop fighting TypeScript, you are on the same side.
- Don't use `any`.
- Avoid using assertions directly, as it can cause unexpected situations.
  - Using the non-null assertion operator is unsafe and will be removed when compiled to JavaScript code. In this case, if the element does not exist, an error occurs. The non-null assertion operator is not recommended unless you really ensure it exists.
  - Using type assertion goes against type inference. A better way is to use the `is` keyword or an assertion function. You should use it only . However, usign `const` assertion is recommended to gives object literals read-only property.
  - (*subjective*) Prefer `as` notation over the angle bracket assertion syntax. It will be easier to retrieve all assertions in the code with a simple search (and check if they can be refactored). Also, it will limit the use of angle brackets to passing type parameters.
- (*subjective*) Use type inference
  - You can omit the type information and use the type inference to automatically provide type information. Type inference does not only take place when initializing variables but also when initializing class members, setting parameter default values, and determining function return types.
  - For initialisation and default value, it might be usefull in specific cases to add the type, if the value provided by the default value is too restrictive.
  - For the function return types, if the inferred type is the same that the one you were expecting, all good. If not, then you should check again the code to see why the function is not working like you think it was... Explicit return types define a contract. But the ultimate contract in the code, which exists at the highest level, is type safety, and this contract is enforced by default from the compiler.
  - It'll also make it easier (safer?) to update the body of the function. If somehow this update results in a breaking change elsewhere in the code, the compiler will flag this immediately. With explicit return types, you no longer have this option. 
  - Proponents of explicit return types will argue that it's good for documentation. Fortunately, in VSCode, you can enable [Inlay Hints](https://github.com/microsoft/vscode-docs/blob/vnext/release-notes/v1_60.md#inlay-hints-for-javascript-and-typescript) to show (as desired) the variable/property/parameter types and the return types of functions that don't have an explicit type annotation.
