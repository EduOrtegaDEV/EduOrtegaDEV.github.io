---
title: "Enum in Typescript is leaky"
date: 2026-08-21T00:00:00
categories:
  - typescript
tags:
  - typescript
  - enums
  - anti-patterns
---

Enums exist. They're not evil. But if you've written a TypeScript enum, you've already lost the argument. Take a look a the following example:

```ts
// This looks fine. It's not.
enum Language {
  CSharp = 'csharp',
  Python = 'python',
  PHP = 'php'
}

function checkLanguage(language: Language) { /* ... */ }

checkLanguage((Language as any).CSharp) //This compiles
```

The type Language is not the union 'csharp' | 'python' | 'php'. It's an enum type, a different thing entirely. TypeScript coerces between them silently, which means:

- There is no static guarantee that callers pass enum members.

You lose the union type's safety on every boundary: API response, JSON parsing, database row, function parameter. The enum type and the string union are two different types that TypeScript lets you mix for free.

## Enums are strings in costumes

An enum member is a string under the hood. At runtime, Language.PHP is 'php'. The only guarantee you have that callers pass enum members is discipline. The type system gave you nothing.

## The real enum pattern in typeScript

```ts
const Language = {
  CSharp: 'csharp',
  Python: 'python',
  PHP: 'php'
} as const

// Derived union type — the real enum
type Language = (typeof Language)[keyof typeof Language];

function checkLanguage(language: Language) { /* ... */ }

checkLanguage('chsarp'); // Error
checkLanguage(Language.CSharp); // Works
checkLanguage('csharp'); // Works
```

## Why this beats enum

- No type coercion hole. The derived union is exactly the values. A string literal that isn't a value fails to type-check, period.
- No in-check trickery. Values are strings, keys are strings. Your data boundary checks out naturally against the union.
- No numeric index map. Object.values(RolesValues) gives you the actual values array.
- String-safe JSON. Serialize/deserialize and validate at the boundary — same pattern every time.

The cost almost nothing. The gain is that typescript type system is working for you, not the other way around.

## Conclusion

Enums in TypeScript are strings with a type system hole. Union types give you the safety you actually want, with less runtime machinery and fewer surprises at every data boundary.

Happy coding!
