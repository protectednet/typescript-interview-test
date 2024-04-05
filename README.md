# TypeScript Interview Test: Dynamic Configuration Object

### Introduction

In this exercise, you'll be challenged to demonstrate your expertise with TypeScript's advanced features, including generics and type safety, as well as your ability to work with dynamic data structures using JavaScript Proxies. The goal is to implement a dynamic configuration manager for a hypothetical application—a component that is used for managing user settings and application configurations flexibly while ensuring type correctness throughout the application. This test is designed to assess your coding skills, problem-solving abilities, and your approach to software design. We're excited to see how you leverage TypeScript to create robust, maintainable, and type-safe solutions.

### Background

Modern applications, particularly those with complex settings or modular configurations, require a delicate balance between strict type checking and the flexibility to accommodate dynamic changes. TypeScript’s static typing helps catch errors early, and JavaScript's Proxy object facilitates dynamic property management, enabling runtime flexibility alongside compile-time safety.

### Task Description

#### Overview

Implement a TypeScript-based configuration manager using generics for compile-time type safety and JavaScript Proxies for dynamic runtime property management. This manager will handle a configuration schema, enabling dynamic access and updates to configuration properties.

#### Example Configuration Schema

Consider the following configuration schema as an example:

```typescript
interface UserConfigInterface
{
  user: {
    name: string,
    age: number,
    email: string
  },
  plan: {
    name: string,
    price: number,
    features: Array<string>
  }
}
```

#### Requirements

1. **Instantiation:** first, we want to be able to create a new instance of the config class, passing through the interface of the configuration schema and an empty config object to initialise it with.

```typescript
const config = Config.create<UserConfigInterface>({});
```

2. **Dynamic Access and Update:** The config instance should typehint for all the properties in the schema recursively. Each "node" on the object tree should typehint for its children and as well as `get` and `set` methods that allow for dynamic access and modification of the value at that node.

```typescript
// Get the user's name
const name = config.user.name.get();
console.log(name); // Output: undefined (because the initial config object passed to the constructor above is empty)

// Set the user's name
config.user.name.set("John McClane");

// See that the user's name has been updated
const updatedName = config.user.name.get();
console.log(updatedName); // Output: "John McClane"

// Get the full user object
const user = config.user.get();
```

3. **Type Inference:**  the `get` and `set` methods should be typehinted correctly based on the type of the property they are accessing. For example, `config.user.name.get()` should be typehinted to return a `string` and `config.plan.price.get()` to return a `number`. Accessing undefined properties or setting incorrect types should generate compile-time errors. Ensure that TypeScript infers types wherever possible, avoiding explicit type annotations to maintain flexibility and leverage TypeScript’s powerful type inference capabilities.

```typescript
const name = config.user.name.get(); // "name" var typehinted as a string
const age = config.user.age.get(); // "age" var typehinted as a number

config.user.name.set(123); // TypeScript error: Argument of type 'number' is not assignable to parameter of type 'string'
config.user.age.set("25"); // TypeScript error: Argument of type 'string' is not assignable to parameter of type 'number'
```

### Success Criteria

Your submission will be evaluated based on the following criteria:

#### Must Haves

- The configuration manager is implemented as a TypeScript class with a static factory method `create` that accepts a generic type parameter for the configuration schema and a single argument for the initial configuration object.
- The configuration manager uses JavaScript Proxies to intercept and manage access to configuration properties dynamically.
- The configuration manager provides `get` and `set` methods for each property in the configuration schema (recursively).
- The `get` method returns the value of the property it is accessing.
- The `set` method updates the value of the property it is setting.
- The `get` method at each node is correctly typehinted to return the correct type of value for the property it is accessing.
- The `set` method is typehinted to accept the correct type of value for the property it is setting.
- A compiler error is thrown when attempting to access a property that doesn't exist in the configuration schema.

#### Nice to Haves

- Write unit tests for the configuration manager class (and any other classes you've created to achieve your solution) to ensure it behaves as expected.
- Code is well-structured, documented, and easy to read and understand. You can use whatever testing framework you like, however, Jest is preferred.

### Submission Requirements

- Commit the project to a public Git repository with a clear commit history.
- Include a README file with instructions on how to set up and run the project, plus any other relevant project details.

## Bonus Feature: Subscribing to Updates

This is an optional feature that you can implement if you have time and want to show off your skills...

### Overview

In addition to the `get` and `set` methods we've added to every node in the tree, let's add a `subscribe` method that allows us to listen to changes on a specific property. This will enable us to react to changes in the configuration object in real-time.

### Requirements

1. **Subscribe Method:** Add a `subscribe` method to each property in the configuration schema (recursively). This method should accept a callback function that is triggered whenever the property is updated.

```typescript
// Subscribe to changes to the user's name
config.user.name.subscribe((newValue) => {
  console.log(`User's name has been updated to: ${newValue}`);
});

// Update the user's name
config.user.name.set("John McClane");
// Output: "User's name has been updated to: John McClane"

// Update the user's name again
config.user.name.set("Hans Gruber");
// Output: "User's name has been updated to: Hans Gruber"
```

2. **Type Inference:** Like with the `get` and `set` methods, the `subscribe` method should be typehinted to accept a callback function with the correct type of value for the property it is listening to.

```typescript
config.user.name.subscribe((newValue) => { // newValue typehinted as a string
  console.log(`User's name has been updated to: ${newValue}`);
});

config.user.name.subscribe((newValue: number) => { // This should cause a compiler error because the "name" property we're trying to subscribe to is a string
  console.log(`User's name has been updated to: ${newValue}`);
});
```

3. **Unsubscribe Capability:** The `subscribe` method should return a function that can be called to unsubscribe from the property changes.

```typescript
// Subscribe to changes to the user's name
const unsubscribe = config.user.name.subscribe((newValue) => {
  console.log(`User's name has been updated to: ${newValue}`);
});

// Update the user's name
config.user.name.set("John McClane");

// Output from our subscriber: "User's name has been updated to: John McClane"

// Unsubscribe from changes to the user's name
unsubscribe();

// Update the user's name again
config.user.name.set("Hans Gruber");

// No output this time as we've cancelled our subscriber
```

### Success Criteria for Bonus Feature

- The configuration manager provides a `subscribe` method for each property in the configuration schema (recursively).
- The `subscribe` method is typehinted to accept a callback function with the correct type of value for the property it is listening to.
- The `subscribe` method returns a function that can be called to unsubscribe from the property changes.
- Callbacks are only triggered when the property they are subscribed to is updated.
