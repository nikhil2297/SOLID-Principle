# SOLID Principles of Architecture

## 1. Single Responsibility Principle (SRP)

The **Single Responsibility Principle** states that a class should have only one reason to change. In other words, a class should only have one responsibility.

### Example: Violating SRP

Suppose you have a class called `UserManager` with two methods:

```typescript
class UserManager {
    authenticateUser() {
        // Authentication logic
    }

    updateUserProfile() {
        // Update user profile logic
    }
}
```

In this case, the `UserManager` class is handling both authentication (which is related to security) and profile updates (which deals with user data). If there is a change in how authentication works, you'd need to modify `authenticateUser()`. Similarly, if user demographics change, you'd need to modify `updateUserProfile()`.

This violates SRP because the class is being modified for two different reasons.

### Fixing SRP

We can refactor this into two separate classes, each with a single responsibility:

```typescript
class AuthManager {
    authenticateUser() {
        // Authentication logic
    }
}

class ProfileManager {
    updateUserProfile() {
        // Update profile logic
    }
}
```
Now, the classes will only change for their specific purposes, making the code easier to maintain and test.

## 2. Open/Closed Principle (OCP)


The **Open/Closed** Principle states that software entities (classes, modules, functions) should be **open for extension but closed for modification**. This means that you should be able to extend a class's behavior without modifying its source code.

### Example: Violating OCP

Let's say you have a Shape class and an AreaCalculator class:

```typescript
class Shape {
    type: string;
    
    constructor(type: string) {
        this.type = type;
    }
}

class AreaCalculator {
    calculateArea(shape: Shape) {
        if (shape.type === 'circle') {
            return 3.14 * 3.14; // Area of a circle
        } else if (shape.type === 'rectangle') {
            return 4 * 5; // Area of a rectangle
        }
        return 0;
    }
}
```

If you introduce a new shape (e.g., a triangle), you'd have to modify the `calculateArea()` method, which violates the OCP.

### Fixing OCP

We can introduce an interface `Shape` and implement it in various classes for each shape:

```typescript
interface Shape {
    calculateArea(): number;
}

class Circle implements Shape {
    radius: number;

    constructor(radius: number) {
        this.radius = radius;
    }

    calculateArea(): number {
        return 3.14 * this.radius * this.radius;
    }
}

class Rectangle implements Shape {
    length: number;
    breadth: number;

    constructor(length: number, breadth: number) {
        this.length = length;
        this.breadth = breadth;
    }

    calculateArea(): number {
        return this.length * this.breadth;
    }
}

class AreaCalculator {
    calculateArea(shape: Shape): number {
        return shape.calculateArea();
    }
}
```

Now, if we want to add a new shape, we just implement the `Shape` interface and don't need to modify `AreaCalculator`.

## 3. Liskov Substitution Principle (LSP)

The *Liskov Substitution Principle* states that objects of a superclass should be replaceable with objects of its subclasses without affecting the correctness of the program.

### Example

Let's say we have an abstract class `Animal` and a subclass `Lion`:

```typescript
abstract class Animal {
    abstract makeSound(): void;
}

class Lion extends Animal {
    makeSound(): void {
        console.log("Roar!");
    }
}

function makeAnimalSound(animal: Animal): void {
    animal.makeSound();
}

const lion: Animal = new Lion();
makeAnimalSound(lion);  // Output: Roar!
```

The `Lion` class can be substituted for the `Animal` class in the `makeAnimalSound()` function, and the program still works correctly. This adheres to the LSP.

## 4. Interface Segregation Principle (ISP)

The **Interface Segregation Principle states** that a client should not be forced to implement interfaces they do not use. In other words, it's better to have multiple smaller, specific interfaces rather than one large, general-purpose interface.

### Example: Violating ISP

Suppose we have an interface `Worker` that includes three methods:

```typescript
interface Worker {
    work(): void;
    eat(): void;
    play(): void;
}

class Developer implements Worker {
    work(): void {
        console.log("Working");
    }

    eat(): void {
        console.log("Eating");
    }

    play(): void {
        console.log("Playing");
    }
}

class Waiter implements Worker {
    work(): void {
        console.log("Serving");
    }

    eat(): void {
        console.log("Eating");
    }

    play(): void {
        // Waiters don't play while working
    }
}
```

In this case, the `Waiter` class is forced to implement the `play()` method, which it doesn't need, violating ISP.

### Fixing ISP

We can break the `Worker` interface into smaller interfaces:

```typescript
interface Workable {
    work(): void;
}

interface Eatable {
    eat(): void;
}

interface Playable {
    play(): void;
}

class Developer implements Workable, Eatable, Playable {
    work(): void {
        console.log("Working");
    }

    eat(): void {
        console.log("Eating");
    }

    play(): void {
        console.log("Playing");
    }
}

class Waiter implements Workable, Eatable {
    work(): void {
        console.log("Serving");
    }

    eat(): void {
        console.log("Eating");
    }
}
```

Now, classes only implement the interfaces they need.

## 5. Dependency Inversion Principle (DIP)

The **Dependency Inversion Principle** states that high-level modules should not depend on low-level modules. Both should depend on abstractions. Additionally, abstractions should not depend on details, but details should depend on abstractions.

### Example: Violating DIP

Imagine a `Room` class that depends on a specific `LightBulb` class:

```typescript
class LightBulb {
    turnOn(): void {
        console.log("LightBulb is on");
    }

    turnOff(): void {
        console.log("LightBulb is off");
    }
}

class Room {
    private bulb: LightBulb;

    constructor() {
        this.bulb = new LightBulb();
    }

    turnOnLight(): void {
        this.bulb.turnOn();
    }

    turnOffLight(): void {
        this.bulb.turnOff();
    }
}
```

The `Room` class depends on the `LightBulb` class, making it hard to switch to a different type of bulb.

### Fixing DIP

We can introduce an abstraction (interface) for the light source:

```typescript
interface LightSource {
    turnOn(): void;
    turnOff(): void;
}

class LightBulb implements LightSource {
    turnOn(): void {
        console.log("LightBulb is on");
    }

    turnOff(): void {
        console.log("LightBulb is off");
    }
}

class Room {
    private light: LightSource;

    constructor(light: LightSource) {
        this.light = light;
    }

    turnOnLight(): void {
        this.light.turnOn();
    }

    turnOffLight(): void {
        this.light.turnOff();
    }
}
```

Now, the Room class depends on the LightSource interface, making it easy to substitute different types of light sources (e.g., LEDLight or CFLBulb).
