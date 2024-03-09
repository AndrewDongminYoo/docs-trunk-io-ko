# how Trunk Check's hold-the-line feature can function as a "superlinter"

**Context:**

You're part of a development team working on a legacy codebase.
There's a widespread practice in the codebase of using `print` statements for logging, which is now considered excessive.
The team decides to clean up the code by removing all unnecessary print statements and standardizing minimal debug logging.
You're tasked with making these changes.

**Before Trunk Check ("Superlinter") Integration:**

1. You write a custom rule or enable a `no-print` rule in your linter to disallow print statements.
2. You go through the code, removing instances of:

   ```javascript
   var p = "Debug info";
   console.log(p); // or print(p) in some languages
   ```

3. You create a pull request (PR) with these changes.
   Now, you have code like:

   ```javascript
   var p = "Debug info";
   // console.log(p); <-- This line is removed in your PR
   ```

4. The CI linter checks the entire codebase and catches an `unused-variable` error because `p` is now unused.
   Your PR fails the automated checks because of this pre-existing issue that only became apparent after your changes.

**After Trunk Check Integration:**

1. The same `no-print` rule is enabled in your linter, and you remove the `console.log` statements.
2. You submit your PR with the same changes as before.
   However, this time, Trunk Check's hold-the-line feature is in play.
3. Trunk Check runs the linter on the codebase before your changes (the upstream) and doesn't report an `unused-variable` error because the variable `p` was used (in the `console.log` statement).
4. It then runs the linter on your changed code (the working tree).
   It detects the new `unused-variable` error caused by your PR since `p` is no longer used.
5. Instead of failing your PR due to a host of unrelated issues, Trunk Check reports precisely that your PR introduced an `unused-variable` error because of the removed `console.log` statement.

**Real-World Example:**

Let's assume the project is a Node.js application.
You've set up a linter configuration that includes a `no-console` rule to disallow `console.log` statements.

```javascript
// Original code
var debugInfo = "User login attempt";
console.log(debugInfo);

// ...
   more code ...
```

Your PR removes the `console.log` statement, resulting in:

```javascript
// Updated code
var debugInfo = "User login attempt";

// ...
   more code ...
```

Trunk Check performs its analysis:

- **Upstream Linting Result:** No issues, `debugInfo` is used in `console.log`.
- **Working Tree Linting Result:** `unused-variable` error detected for `debugInfo`.

**Outcome with Trunk Check:**

You get a focused report from Trunk Check:

```log
File: user-login.js
Line 1: `unused-variable` error: The variable `debugInfo` is defined but never used.
```

This pinpoint accuracy allows you to correct the specific issue by removing the unused variable or using it in a meaningful way.
The "superlinter" capability of Trunk Check is clear: it allows developers to improve the codebase incrementally without being overwhelmed by a deluge of unrelated issues.
It respects the historical context of the code while maintaining a high standard for new changes.
