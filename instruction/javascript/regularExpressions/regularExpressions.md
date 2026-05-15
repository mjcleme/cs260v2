# JavaScript regular expressions

📖 **Deeper dive reading**: [MDN Regular expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)

Regular expression support is built right into JavaScript. If you are not familiar with regular expressions, you can think of them as textual pattern matchers. You use a regular expression to find text in a string so that you can replace it, or simply to know that it exists.

You can create a regular expression using the class constructor or a regular expression literal.

```js
const objRegex = new RegExp('ab*', 'i');
const literalRegex = /ab*/i;
```

The `string` class has several functions that accept regular expressions. This includes `match`, `replace`, `search`, and `split`. For a quick test to see if there is a match you can use the regular expression object's `test` function.

```js
const petRegex = /(dog)|(cat)|(bird)/gim;
const text = 'Both cats and dogs are pets, but not rocks.';

text.match(petRegex);
// RETURNS: ['cat', 'dog']

text.replace(petRegex, 'animal');
// RETURNS: Both animals and animals are pets, but not rocks.

petRegex.test(text);
// RETURNS: true
```


## Advanced Flags
JavaScript regular expressions use flags to modify search behavior.

| Flag | Name | Description |
|-|-|-|
| `g` | Global | Finds all matches rather than stopping after the first. |
| `i` | Case-insensitive | Ignores case (e.g., `/[a-z]/i` matches "A"). |
| `m` | Multiline | `^` and `$` match start/end of lines, not just start/end of string. |
| `s` | DotAll | Allows `.` to match newline characters (`\n`). |
| `u` | Unicode | Enables full Unicode support and handles surrogate pairs. |
| `y` | Sticky | Matches only from the index indicated by the `lastIndex` property. |


## Assertions: Lookahead and Lookbehind
Assertions match a position rather than a character, based on what follows or precedes it.

*   **Positive Lookahead (`(?=...)`):** Matches a group after the main expression without including it in the result.
    *   `/\d+(?=%)/` matches "100" in "100%".
*   **Negative Lookahead (`(?!...)`):** Matches only if the main expression is **not** followed by the group.
    *   `/\d+(?!%)/` matches "100" in "100 dollars".
*   **Positive Lookbehind (`(?<=...)`):** Matches a group before the main expression.
    *   `/(?<=\$)\d+/` matches "100" in "$100".
*   **Negative Lookbehind (`(?<!...)`):** Matches only if the main expression is **not** preceded by the group.
    *   `/(?<!\$)\d+/` matches "100" in "€100".


## Capturing Groups and Backreferences
Groups allow you to treat multiple characters as a single unit and extract sub-matches.

*   **Capturing Groups `(...)`:** Parentheses create a group that can be accessed later.
*   **Named Capturing Groups `(?<name>...)`:** Assigns a name to a group for easier access via the `groups` property in the result.
    ```javascript
    const regex = /(?<year>\d{4})-(?<month>\d{2})/;
    const match = regex.exec("2023-10");
    console.log(match.groups.year); // "2023"
    ```
*   **Non-capturing Groups `(?:...)`:** Groups characters for quantification or alternation but does not "remember" the match, improving performance.
*   **Backreferences (`\n`):** Refers back to a previously captured group within the same regex.
    *   `/(['"])\w+\1/` matches `"hello"` or `'hello'`, ensuring the opening and closing quotes match.


## String vs. RegExp Methods
JavaScript provides methods on both the `String` prototype and the `RegExp` prototype.

### RegExp Methods
*   `regex.test(string)`: Returns `true` or `false`.
*   `regex.exec(string)`: Returns an array of information or `null`. Essential for looping through global matches.

### String Methods
*   `str.match(regex)`: Returns an array of matches.
*   `str.matchAll(regex)`: Returns an iterator of all matches, including capturing groups (requires `g` flag).
*   `str.replace(regex, replacement)`: Replaces matches with a string or the result of a function.
*   `str.search(regex)`: Returns the index of the first match, or `-1`.
*   `str.split(regex)`: Breaks a string into an array using the regex as a delimiter.


## The `lastIndex` Property
When using the global (`g`) or sticky (`y`) flags with `exec()` or `test()`, the RegExp object maintains state in the `lastIndex` property.

```javascript
const regex = /hi/g;
const str = "hi hi";

console.log(regex.test(str)); // true
console.log(regex.lastIndex); // 2 (index where the next search starts)
console.log(regex.test(str)); // true
console.log(regex.lastIndex); // 5
console.log(regex.test(str)); // false (resets to 0)
```
**Warning:** Reusing the same global regex instance on different strings without resetting `lastIndex = 0` can lead to unexpected results.


## Unicode Property Escapes
With the `u` flag, you can match characters based on their Unicode properties (e.g., scripts, emojis, or punctuation).

*   `\p{Property}`: Matches characters with a specific property.
*   `\P{Property}`: Negation of `\p`.

```javascript
// Matches any character in the Greek script
const greekRegex = /\p{Script=Greek}/u;
greekRegex.test("π"); // true

// Matches any emoji/symbol
const symbolRegex = /\p{S}/u;
symbolRegex.test("🚀"); // true
```


## Character Classes Shortcuts
| Shortcut | Equivalent | Description |
|-|-|-|
| `\d` | `[0-9]` | Any digit |
| `\D` | `[^0-9]` | Any non-digit |
| `\w` | `[A-Za-z0-9_]` | Alphanumeric "word" character |
| `\W` | `[^A-Za-z0-9_]` | Any non-word character |
| `\s` | `[ \f\n\r\t\v...]` | Any whitespace (space, tab, line break) |
| `\S` | `[^ \f\n\r\t\v...]` | Any non-whitespace |
