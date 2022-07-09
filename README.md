# ECMAScript Catch Guards

This proposal adds the catch guards to the language, enabling developers to catch
exceptions only when they match a specific class or set of classes.

This proposal draws heavily from similar features available in
[Java](https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.20),
[C#](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/try-catch),
[Ruby](http://rubylearning.com/satishtalim/ruby_exceptions.html), and
[Python](https://docs.python.org/3/tutorial/errors.html#handling-exceptions).

## Problem statement
Javascript doesn't provide an ergonomic way of treating errors by type.

## Status

**Champion**: 
Willian Martins (Netflix, [@wmsbill](https://twitter.com/wmsbill)),


**Stage**: 0

**Lastest update**: draft created

## Motivation

- **Developer ergonomics**:
  
  Today, developers have to catch all Errors, add an `if` or `switch` statement and 
  rethrow even when they want to catch one particular type of error:

  ```javascript
  class ConflictError extends Error {}
  
  try {
    something();
  } catch (err) {
    if (err instanceOf ConflictError) {
      // handle it...
    }
    throw err;
  }
  ```
  
- **Parity with other languages**:
  
  Scoping error handling to specific types is a common construct in several programming
  languges, as:
  [Java](https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.20),
  [C#](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/try-catch),
  [Ruby](http://rubylearning.com/satishtalim/ruby_exceptions.html), and
  [Python](https://docs.python.org/3/tutorial/errors.html#handling-exceptions).

  
- **Code/Engine Optimization**:

  Engines will be able to skip the block entirely if the error doesn't match the correct type.
  
## Syntax options

The current implementation uses the following syntax:
```
 catch ( CatchParameter[?Yield, ?Await] ) { block }
```

Where `CatchParameter` is:
* `BindingIdentifier[?Yield, ?Await]`
* `BindingPattern`

This proposal introduces a new Binding type for the Catch statement called `BindingGuard`; these are some of the options for this new type.

### Option 1 (using `as`):

```javascript
class ConflictError extends Error {}
class NotFoundError extends Error {}
class OtherError extends Error {}
  
try {
  something();
} catch (ConflictError as conflict) {
  // handle it one way...
} catch(NotFoundError || OtherError as other) {
  // handle the other way...
} catch (err) {
  // catch all...
}
```

### Option 2 (using `if instanceOf`):

```javascript
class ConflictError extends Error {}
class NotFoundError extends Error {}
class OtherError extends Error {}
  
try {
  something();
} catch (conflict if instanceOf ConflictError) {
  // handle it one way...
} catch(other if instanceOf NotFoundError || instanceOf OtherError) {
  // handle the other way...
} catch (err) {
  // catch all...
}
```

### Option 3 (using `instanceOf`):

```javascript
class ConflictError extends Error {}
class NotFoundError extends Error {}
class OtherError extends Error {}
  
try {
  something();
} catch (conflict instanceOf ConflictError) {
  // handle it one way...
} catch(other instanceOf NotFoundError || instanceOf OtherError) {
  // handle the other way...
} catch (err) {
  // catch all...
}
```

### Option 4 (using `:`):
Possible conflict with Types as comment proposal

```javascript
class ConflictError extends Error {}
class NotFoundError extends Error {}
class OtherError extends Error {}
  
try {
  something();
} catch (conflict: ConflictError) {
  // handle it one way...
} catch(other: NotFoundError | OtherError) {
  // handle the other way...
} catch (err) {
  // catch all...
}
```

## Implementations

* Babel Plugin //TBD

## Q&A

#### What if the error is a string?

We are trying to solve for types only, so this would work:

```javascript
try {
  something();
} catch (String as err) {
  // handle it...
}
```

But it would catch all errors thrown as strings.
