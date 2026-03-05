---
applyTo: "**"
---
# Clean Code Principles

## Functions

### Small Functions
**"The first rule of functions is that they should be small. The second rule is that they should be smaller than that."**

- Functions should be 5-15 lines ideally
- If you need to scroll, it's too long
- Extract until you can't extract anymore

### Do One Thing
**"Functions should do one thing. They should do it well. They should do it only."**

- If a function does more than one thing, extract the parts into separate functions
- A function does "one thing" if you cannot meaningfully extract another function from it

### One Level of Abstraction
**"Statements within a function should all be at the same level of abstraction."**

- Don't mix high-level concepts with low-level details
- Read top-to-bottom like a narrative

**Bad:**
```typescript
function processUser(userId) {
  const user = database.query('SELECT * FROM users WHERE id = ?', userId);  // Low-level
  if (user.isActive) {  // High-level
    const hash = crypto.createHash('sha256').update(user.password).digest('hex');  // Low-level
    sendEmail(user);  // High-level
  }
}
```

**Good:**
```typescript
function processUser(userId) {
  const user = getUser(userId);
  if (user.isActive) {
    hashPassword(user);
    sendWelcomeEmail(user);
  }
}
```

### Function Arguments
**"The ideal number of arguments for a function is zero. Next comes one, followed closely by two. Three arguments should be avoided where possible. More than three requires very special justification."**

- **Niladic (0 args)** - Best
- **Monadic (1 arg)** - Good
- **Dyadic (2 args)** - OK, but consider refactoring
- **Triadic (3 args)** - Avoid if possible
- **Polyadic (4+ args)** - Should be very rare; use object instead

**Bad:**
```typescript
function createUser(firstName, lastName, email, age, country, city, zipCode) {
  // 7 arguments - too many!
}
```

**Good:**
```typescript
interface UserData {
  firstName: string;
  lastName: string;
  email: string;
  age: number;
  address: {
    country: string;
    city: string;
    zipCode: string;
  };
}

function createUser(userData: UserData) {
  // 1 argument
}
```

### No Side Effects
**"Functions should have no side effects."**

- If a function is named `checkPassword()`, it should not log the user in
- Side effects create temporal couplings and order dependencies
- Use Command Query Separation

### Command Query Separation
**"Functions should either do something or answer something, but not both."**

- **Commands** change state, return void
- **Queries** return data, don't change state

**Bad:**
```typescript
function setAndCheckName(name: string): boolean {
  this.name = name;  // Command
  return this.name === 'admin';  // Query
}

if (setAndCheckName('admin')) { }  // Confusing!
```

**Good:**
```typescript
function setName(name: string): void {
  this.name = name;
}

function isAdmin(): boolean {
  return this.name === 'admin';
}

setName('admin');
if (isAdmin()) { }
```

### Don't Repeat Yourself (DRY)
**"Duplication may be the root of all evil in software."**

- Extract duplicated code into functions
- But avoid over-abstraction (premature DRY is also bad)
- **Rule of Three**: duplicate twice, extract on third occurrence

---

## Naming

### Use Intention-Revealing Names
**"Names should reveal intent."**

- `elapsedTimeInDays` not `d`
- `getUserByEmail` not `get`
- If a name requires a comment, it's a bad name

### Avoid Disinformation
**"Programmers must avoid leaving false clues that obscure the meaning of code."**

- Don't call something `accountList` if it's not actually a List
- Avoid names that vary only slightly

### Make Meaningful Distinctions
**"If names must be different, then they should also mean something different."**

- Don't use `a1, a2, a3`
- Don't use noise words: `ProductInfo` vs `ProductData` vs `Product`
- Don't use prefixes: `theProduct`, `aProduct`

### Use Pronounceable Names
**"If you can't pronounce it, you can't discuss it without sounding like an idiot."**

- `generationTimestamp` not `genymdhms`
- `customerId` not `custId`

### Use Searchable Names
**"Single-letter names can ONLY be used as local variables inside short methods."**

- `MAX_CLASSES_PER_STUDENT` not `7`
- `employeeCount` not `e`

### Class Names
**"Classes and objects should have noun or noun phrase names."**

- `Customer`, `WikiPage`, `Account`, `AddressParser`
- NOT `Manager`, `Processor`, `Data`, `Info`
- Avoid verbs

### Method Names
**"Methods should have verb or verb phrase names."**

- `postPayment`, `deletePage`, `save`
- Accessors, mutators, and predicates: `getName`, `setName`, `isPosted`

---

## Comments

**"Don't comment bad code—rewrite it."**

### Good Comments (Rare)
- Legal comments (copyright, licenses)
- Informative comments explaining intent
- Warnings of consequences
- TODO comments (but clean them up regularly)
- Public API documentation

### Bad Comments (Avoid)
- **Redundant comments** - "// increment i" above `i++`
- **Misleading comments** - outdated or incorrect
- **Journal comments** - change history (use git)
- **Noise comments** - state the obvious
- **Commented-out code** - DELETE IT (it's in git)
- **Closing brace comments** - if you need them, your function is too long

**Never:**
```typescript
// Check to see if the employee is eligible for full benefits
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))
```

**Instead:**
```typescript
if (employee.isEligibleForFullBenefits())
```

---

## Error Handling

### Use Exceptions Rather Than Return Codes
**"Return codes clutter the caller."**

**Bad:**
```typescript
const result = deleteUser(userId);
if (result === ERROR_CODES.NOT_FOUND) {
  // handle error
}
```

**Good:**
```typescript
try {
  deleteUser(userId);
} catch (UserNotFoundError) {
  // handle error
}
```

### Extract Try/Catch Blocks
**"Try/catch blocks are ugly in their own right. Extract them."**

**Bad:**
```typescript
function deleteUser(userId) {
  try {
    const user = database.find(userId);
    database.delete(user);
    logger.log('Deleted user');
  } catch (error) {
    logger.error(error);
  }
}
```

**Good:**
```typescript
function deleteUser(userId) {
  try {
    performDelete(userId);
  } catch (error) {
    logError(error);
  }
}

function performDelete(userId) {
  const user = database.find(userId);
  database.delete(user);
  logger.log('Deleted user');
}
```

### Don't Return Null
**"Returning null is bad. Throwing exceptions is better. Returning a special case object is even better."**

**Bad:**
```typescript
function getUser(id) {
  const user = database.find(id);
  return user || null;  // Forces caller to check
}

const user = getUser(123);
if (user !== null) {  // Every caller must check
  user.getName();
}
```

**Good:**
```typescript
function getUser(id) {
  const user = database.find(id);
  if (!user) throw new UserNotFoundError();
  return user;
}

// Or return a Special Case object (Null Object Pattern)
class NullUser {
  getName() { return 'Guest'; }
}

function getUser(id) {
  return database.find(id) || new NullUser();
}
```

---

## Practical Rules

### Boy Scout Rule
**"Leave the code cleaner than you found it."**

- Improve code every time you touch it
- Even small improvements add up

### The Principle of Least Surprise
**"Any function or class should implement the behaviors that another programmer could reasonably expect."**

- `add()` should add, not delete
- `getUser()` should return a user, not modify one

### No Magic Numbers
**"Replace magic numbers with named constants."**

**Bad:**
```typescript
if (age > 65) { }
setTimeout(fn, 86400000);
```

**Good:**
```typescript
const RETIREMENT_AGE = 65;
if (age > RETIREMENT_AGE) { }

const ONE_DAY_IN_MS = 86400000;
setTimeout(fn, ONE_DAY_IN_MS);
```

### Avoid Negative Conditionals
**"Negatives are harder to understand than positives."**

**Bad:**
```typescript
if (!isNotActive) { }
```

**Good:**
```typescript
if (isActive) { }
```

---

## Code Smells to Avoid

1. **Long Method** - Extract smaller methods
2. **Large Class** - Split into multiple classes
3. **Long Parameter List** - Use object or split method
4. **Divergent Change** - One class changes for multiple reasons
5. **Shotgun Surgery** - One change requires modifications in many classes
6. **Feature Envy** - Method uses more features of another class than its own
7. **Data Clumps** - Same group of data appears together repeatedly
8. **Primitive Obsession** - Using primitives instead of small objects
9. **Switch Statements** - Often indicate Open/Closed violation
10. **Lazy Class** - Class doesn't do enough to justify existence
11. **Speculative Generality** - "We might need this someday"
12. **Message Chains** - `a.getB().getC().getD()`
13. **Middle Man** - Class that delegates most of its work
14. **Data Class** - Class with only getters/setters
15. **Comments** - Often indicate bad code that needs refactoring

---