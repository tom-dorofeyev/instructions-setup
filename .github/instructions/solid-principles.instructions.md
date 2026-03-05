---
applyTo: "**"
---
# SOLID Principles (Robert C. Martin)

## S - Single Responsibility Principle (SRP)

**"A class should have one, and only one, reason to change."**

- Each module/function should do ONE thing and do it well
- If you need "and" to describe what it does, it's doing too much
- A responsibility is a "reason to change"

**Bad:**

```typescript
function saveUserAndSendEmail(user) {
  database.save(user);
  emailService.send(user.email, "Welcome!");
}
```

**Good:**

```typescript
function saveUser(user) {
  database.save(user);
}

function sendWelcomeEmail(user) {
  emailService.send(user.email, "Welcome!");
}
```

---

## O - Open/Closed Principle (OCP)

**"Software entities should be open for extension, but closed for modification."**

- Add new functionality by extending, not by modifying existing code
- Use abstractions (interfaces, abstract classes) to allow new implementations
- Avoid long if/else or switch statements for type checking

**Bad:**

```typescript
function calculateArea(shape) {
  if (shape.type === "circle") return Math.PI * shape.radius ** 2;
  if (shape.type === "square") return shape.side ** 2;
  // Adding triangle requires modifying this function
}
```

**Good:**

```typescript
interface Shape {
  calculateArea(): number;
}

class Circle implements Shape {
  calculateArea() {
    return Math.PI * this.radius ** 2;
  }
}

class Square implements Shape {
  calculateArea() {
    return this.side ** 2;
  }
}
// Add Triangle without modifying existing code
```

---

## L - Liskov Substitution Principle (LSP)

**"Derived classes must be substitutable for their base classes."**

- Subclasses should strengthen, not weaken, the base class contract
- If S is a subtype of T, then objects of type T can be replaced with objects of type S
- Don't override methods to throw "not implemented" errors

**Bad:**

```typescript
class Bird {
  fly() {
    /* implementation */
  }
}

class Penguin extends Bird {
  fly() {
    throw new Error("Penguins cannot fly");
  } // Violates LSP
}
```

**Good:**

```typescript
interface Bird {
  move(): void;
}

class FlyingBird implements Bird {
  move() {
    this.fly();
  }
  private fly() {
    /* implementation */
  }
}

class Penguin implements Bird {
  move() {
    this.swim();
  }
  private swim() {
    /* implementation */
  }
}
```

---

## I - Interface Segregation Principle (ISP)

**"Many client-specific interfaces are better than one general-purpose interface."**

- Don't force clients to depend on interfaces they don't use
- Split large interfaces into smaller, focused ones
- Clients should only know about methods they actually call

**Bad:**

```typescript
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
}

class Robot implements Worker {
  work() {
    /* ok */
  }
  eat() {
    throw new Error("Robots don't eat");
  }
  sleep() {
    throw new Error("Robots don't sleep");
  }
}
```

**Good:**

```typescript
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

interface Sleepable {
  sleep(): void;
}

class Robot implements Workable {
  work() {
    /* implementation */
  }
}

class Human implements Workable, Eatable, Sleepable {
  work() {
    /* implementation */
  }
  eat() {
    /* implementation */
  }
  sleep() {
    /* implementation */
  }
}
```

---

## D - Dependency Inversion Principle (DIP)

**"Depend upon abstractions, not concretions."**

- High-level modules should not depend on low-level modules; both should depend on abstractions
- Abstractions should not depend on details; details should depend on abstractions
- Inject dependencies rather than creating them internally

**Bad:**

```typescript
class UserService {
  private db = new MySQLDatabase(); // Concrete dependency

  getUser(id: string) {
    return this.db.query(`SELECT * FROM users WHERE id = ${id}`);
  }
}
```

**Good:**

```typescript
interface Database {
  query(sql: string): any;
}

class UserService {
  constructor(private db: Database) {} // Depends on abstraction

  getUser(id: string) {
    return this.db.query(`SELECT * FROM users WHERE id = ${id}`);
  }
}

// Can inject any Database implementation
const service = new UserService(new MySQLDatabase());
const testService = new UserService(new InMemoryDatabase());
```

---

## Quick Reference

| Principle | Question to Ask                                               |
| --------- | ------------------------------------------------------------- |
| **SRP**   | Does this class/function have more than one reason to change? |
| **OCP**   | Can I add new behavior without modifying existing code?       |
| **LSP**   | Can I substitute a subclass without breaking functionality?   |
| **ISP**   | Am I forcing clients to depend on methods they don't use?     |
| **DIP**   | Am I depending on abstractions or concrete implementations?   |
