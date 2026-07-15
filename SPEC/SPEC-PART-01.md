# .nd Language Specification - Part 1: Grammar & Lexical Structure  

## 0. Conformance

> Don't ask questions whose answers are obvious from the design

This document defines the definitive lexical and syntactic grammar for .nd source text.

​To implement a full compiler or interpreter pipeline, this specification must be paired with its companion documents:
- ​**Static Semantics:** Governs type checking, name binding, and scope resolution. (see: [Static Semantics specification]())
- **​Runtime Semantics:** Governs bytecode generation, frame execution, and standard library behavior. (see: [Runtime Semantics specification]())

​Syntax verification occurs entirely before static-semantic analysis. The compiler must abort with a syntax error upon encountering any token sequence not defined by this grammar.

### Notation  
Grammar productions in this document use the EBNF notation defined
by the [W3C XML 1.0 (Fifth Edition) specification, §6](https://www.w3.org/TR/xml/#sec-notation).


## 1. Source Form 

### 1.1 Encoding
'.nd' source files are UTF-8 encoded. 

If a source file contains invalid UTF-8 byte sequences, the compiler must reject the file immediately with a source-encoding error and abort before tokenization begins.

### 1.2 Whitespace
- The space character (U+0020) is the only significant whitespace character for token separation within a line.  
- Leading whitespace determines indentation level.  
- The tab character (U+0009) is not permitted in leading whitespace.  
- A line beginning with a tab is a lexical error.  
- Tabs may only appear inside string literals.

### 1.3 Line Endings
The following terminators are supported:
- LF (\n)
- CRLF (\r\n)  

They are normalized to a single \n during tokenization. A lone CR (\r) not followed by LF is a lexical error.

### 1.4 Comments

```ebnf
Comment ::= '//' [^\n]*
```

A line comment begins with // and continues until (but does not include) the line terminator.

Discard everything from `//` up to the line terminator during tokenization.

Comments are not recognized inside string literals. // inside a string is treated as literal characters.

<details>
<summary>Example: Comments</summary>

```nd
// through goes Hamilton!!!
// Comments can be single line or multiline
// and multiline is just multiple singleline
@button
  text "Click me"                  // click him
  text "http://www.github.com"     // not a comment
```

</details>

## 2. Lexical Grammar

### 2.1 Token Categories

The lexer produces a stream of tokens in the following categories. Comments (§1.4) and non-indentation whitespace are consumed by the lexer and do not appear in the token stream.

| Category | Examples | Defined in |
|----------|----------|------------|
| Identifier | `count`, `post`, `card` | §2.2 |
| Node Sigil | `@` | §2.2 |
| Reserved Word | `var`, `if`, `each`, `import` | §2.3 |
| Integer | `200`, `-32`, `0` | §2.4 |
| Float | `0.08`, `-0.1` | §2.4 |
| Color | `0x0055ffff` | §2.5 |
| String | `"hello"`, multi-line, interpolated | §2.6 |
| Punctuation | `(`, `)`, `[`, `]`, `{`, `}`, `.`, `,` | §2.7, §2.8 |
| Operator | `+`, `==`, `?`, `??`, `\|`, `..` | §2.8 |
| INDENT / DEDENT | (synthetic, no source text) | §2.9 |
| NEWLINE | (synthetic, statement separator) | §2.9 |
| EOF | (synthetic, end of token stream) | — |

### 2.2 Identifiers

```ebnf
Identifier ::= IdentStart IdentContinue*
IdentStart ::= [a-zA-Z]
IdentContinue ::= [a-zA-Z0-9_]
```

An identifier is a sequence of letters, digits, and underscores, beginning only with a letter. Identifiers are case-sensitive.

The `@` sigil (U+0040) is a separate token (§2.1) and is not part
of an identifier. `@name` is a grammar-level combination (§3.2), not
a single lexical token.

An identifier matching a reserved word (§2.3) is tokenized as the
reserved word, not as `Identifier`.

<details>
<summary>Example</summary>

```nd
@card
@big_number_two_eventy_wan_k
@abstain_island
```

Here `card`, `big_number_two_eventy_wan_k`, and `abstain_island` are each tokenized as
`Identifier`, preceded by a separate `@` token.
</details>

### 2.3 The Wildcard

```ebnf
Wildcard ::= '_'
```

The single underscore `_` (U+005F) is a unique lexical token that represents a wildcard pattern.

The wildcard acts as a discard marker. It accepts any value syntactically but does not create a variable binding. Attempting to reference `_` as a variable is a static‑semantic error.

**The Distinction Between identifier Production and The Wildcard**  
Because the Identifier production (§2.2) requires starting with a letter, a solitary underscore cannot match as an identifier. The lexer recognise `_` and emits a dedicated Wildcard token, free of conflict.


### 2.4 Binding Targets

```ebnf
BindingTarget ::= Identifier | Wildcard
```

A BindingTarget is a syntactic abstraction that represents any location in the grammar where a value is introduced, either bound to a named variable or explicitly discarded.

<details>
<summary>Reasoning: Binding Targets</summary>

By separating this concept into its own named production, the grammar establishes a modular boundary for value resolution. Any downstream compiler implementation can treat BindingTarget as a unified AST node, ensuring that future extensions to the language’s binding mechanics require zero structural modifications to dependent rules like MapEntry.

</details/>


### 2.5 Reserved Words

Reserved words cannot be used as `Identifier` (§2.2). A token
matching a reserved word is classified as `ReservedWord`, not
`Identifier`, regardless of position.

See: [Appendix B: Reserved Word List](#appendixbreservedwordlist).

### 2.6 Numeric Literals  

```ebnf
NumericLiteral ::= FloatLiteral | IntLiteral
IntLiteral     ::= [0-9]+
FloatLiteral   ::= [0-9]+ '.' [0-9]+

```
The lexer classifies numbers as positive magnitudes.
 
Negative values are produced *syntactically* when a NumericLiteral is preceded by a unary negation operator `-`, which is resolved and folded into a signed binary value during [static analysis](static-analysis), *not* during lexing or parsing. 
 
The presence of a fractional period `.` explicitly designates a FloatLiteral. 

<details>

<summary>Example: Tokenization of Numeric Literals</summary>

```nd
var x 12          // IntLiteral(12)
var y -12         // Operator(-), IntLiteral(12)
var z 0.08        // FloatLiteral(0.08)
```
</details>

### 2.7 Hexadecimal Literals

```ebnf
HexLiteral   ::= '0x' HexDigit{8}
HexDigit     ::= [0-9a-fA-F]
```

A HexLiteral represents an unsigned 32-bit integer value expressed as a base-16 magnitude. It begins with 0x followed by exactly eight hexadecimal digits.

Because HexLiteral shares its leading digit 0 with IntLiteral and FloatLiteral (§2.4), the lexer resolves this branch using standard LL(1) lookahead:
- On encountering '0', it inspects the immediate next character.
- If the character is 'x' or 'X', it commits to HexLiteral and consumes exactly eight hexadecimal digits.
- ​Any other character leaves the token in the IntLiteral or FloatLiteral pipeline.

Hexadecimal letters are case-insensitive: `0x0055FFFF` and `0x0055ffff` are the same literal.

<details>
<summary>Example: Tokenization of Hexadecimal Literals</summary>

```nd
var mask 0x0055ffff   // HexLiteral(0x0055ffff), strict 8 digits, 32-bit value
paint 0x0055FFFF      // HexLiteral(0x0055ffff), resolved as a color value by the 'paint' content verb.
```

</details>

### 2.8 String Literals

```ebnf
StringLiteral ::= '"' RawContent? '"'
RawContent    ::= StringChar+
StringChar    ::= [^"\\{}] | EscapeSequence | Interpolation
```

A string literal begins and ends with double quotes `"`. 
The lexer isolates the raw text stream and computes the normalized value using a three-step pass:
 1. A leading newline (\n) immediately following the opening `"` is discarded.
 2. A trailing newline (\n) immediately preceding the closing `"` is discarded.
 3. The least-indented, non-blank line establishes the reference indentation. This exact number of leading spaces is stripped uniformly from all lines. Blank lines are unaffected.

This normalization pass executes vacuously as a no-op for single-line strings containing no internal newlines.

​A string literal may contain Markdown formatting (such as \*emphasis\* or \`verbatim\`). The lexer completely ignores this formatting, treating and preserving all Markdown syntax patterns as ordinary, *uninterpreted* characters.

<details>
<summary>Example: String Literals</summary>

```nd
text "Hello." 
// clean_value: "Hello."

text "
  First `paragraph`.

  Second paragraph, **still here.**
"
// clean_value: "First `paragraph`.\n\nSecond paragraph, **still here.**"
```
</details>

#### 2.8.1 Escape Sequences

```ebnf
EscapeSequence ::= '\' ('"' | '\' | 'n' | 't' | '{' | '}')
```

Upon encountering an unescaped backslash (\), the lexer consumes the immediate next character literally as *content*, bypassing string termination or interpolation triggers.

Supported escape mappings include \", \\, \n, \t, \{, and \}. Any backslash followed by a character outside this set is a lexical error.

<details>
<summary>Examples: String Literals - Escape Sequences</summary>

```nd
text "She said \"go\" and left."
// clean_value: "She said "go" and left."

text "Use \\n for a literal backslash-n."
// clean_value: "Use \n for a literal backslash-n."

text "Curly braces: \{not interpolated\}"
// clean_value: "Curly braces: {not interpolated}"

text "Invalid: \Alonso" 
// lexical error: 'A' is not a valid escape target
```
</details>

#### 2.8.2 Interpolation

```ebnf
Interpolation ::= '{' Expression '}'
```

An unescaped `{` inside a string marks the beginning of an interpolation block. The enclosed text is evaluated as an Expression (§3.5). The matching unescaped `}` at the same nesting depth terminates the interpolation block, resuming string-content scanning.

Because StringLiteral and Expression are mutually recursive productions, interpolation blocks may contain nested string literals or nested brace pairs (such as map literals) without interfering with the outer string's own structure.

<details>
<summary>Examples: String Literals - String Interpolation</summary>

```nd
text "Hello, {user.name}!"
text "Result: {count > 10 ? "many" : "few"}"
```
The second example contains nested string literals ("many", "few") inside the interpolation block, which are tokenized independently of the outer string's bounding quotes.
</details>

### 2.9 Array & Map Literals

#### 2.9.1 Array Literals

```ebnf
ArrayLiteral ::= '[' (Expression (' ' Expression)*)? ']'
```

An ArrayLiteral represents an ordered sequence of values enclosed in square brackets `[` and `]`. Elements within an array are separated by whitespace (not commas).

<details>
<summary>Examples: Array Literals</summary>
  
```nd
// Array literal
var tags ["work" "life" "art"]
```
</details>

#### 2.9.2 Map Literals

```ebnf
MapLiteral   ::= '{' (MapEntry (',' MapEntry)*)? '}'
MapEntry     ::= BindingTarget Expression
```

A MapLiteral represents an ordered set of key-value associations enclosed in curly braces `{` and `}`. Map entries are separated by commas `,`. Each entry consists of a key, which must be a BindingTarget, followed by its value Expression.

<details>
<summary>Examples: Map Literals</summary>
  
```nd
// Map literal
var priority_color {
  low    0x2ecc71ff,
  medium 0xf39c12ff,
  high   0xe74c3cff,
  _      0xccccccff
}

```
</details>

### 2.10 Operators & Punctuation

### 2.11 Indentation (INDENT/DEDENT token rule)

## 3. Syntactic Grammar

### 3.1 Program Structure

### 3.2 Node Declaration

### 3.3 Property Line

### 3.4 Content Verbs

### 3.5 Expressions

#### 3.5.1 Precedence Table

#### 3.5.2 Operators

#### 3.5.3 Function Calls

#### 3.5.4 Lambda Form

### 3.6 Control Constructs ('if'/'else', 'on', 'each')

### 3.7 Declarations ('var', 'data', 'task', 'import', 'global')

### 3.8 Component Definitions (parameters, 'slot')

### 3.9 World Declarations


## 4. Disambiguation Rules

### 4.1 Property vs. Child Node

### 4.2 'each' Collection-form vs. Range-form

### 4.3 [others as found]


## Appendix A. Full EBNF Grammar (consolidated)


## Appendix B. Reserved Word List
The following tokens are unconditionally reserved in all `.nd` files:

<table>
  <thead>
    <tr><th>Category</th><th>Token</th><th>Notes</th></tr>
  </thead>
  <tbody>
    <tr><td rowspan="6"><strong>Control flow</strong></td><td><code>if</code></td><td></td></tr>
    <tr><td><code>else</code></td><td></td></tr>
    <tr><td><code>on</code></td><td></td></tr>
    <tr><td><code>each</code></td><td></td></tr>
    <tr><td><code>stop</code></td><td></td></tr>
    <tr><td><code>return</code></td><td></td></tr>
    <tr><td rowspan="3"><strong>State</strong></td><td><code>var</code></td><td></td></tr>
    <tr><td><code>data</code></td><td></td></tr>
    <tr><td><code>task</code></td><td></td></tr>
    <tr><td rowspan="3"><strong>Structure</strong></td><td><code>import</code></td><td></td></tr>
    <tr><td><code>global</code></td><td></td></tr>
    <tr><td><code>slot</code></td><td></td></tr>
    <tr><td rowspan="4"><strong>Operators</strong></td><td><code>and</code></td><td></td></tr>
    <tr><td><code>or</code></td><td></td></tr>
    <tr><td><code>not</code></td><td></td></tr>
    <tr><td><code>as</code></td><td></td></tr>
    <tr><td rowspan="3"><strong>Literals</strong></td><td><code>true</code></td><td></td></tr>
    <tr><td><code>false</code></td><td></td></tr>
    <tr><td><code>null</code></td><td></td></tr>
    <tr><td rowspan="7"><strong>Properties</strong></td><td><code>pos</code></td><td></td></tr>
    <tr><td><code>size</code></td><td></td></tr>
    <tr><td><code>type</code></td><td></td></tr>
    <tr><td><code>radius</code></td><td></td></tr>
    <tr><td><code>font</code></td><td></td></tr>
    <tr><td><code>surface</code></td><td></td></tr>
    <tr><td><code>scroll</code></td><td></td></tr>
    <tr><td rowspan="4"><strong>Content verbs</strong></td><td><code>text</code></td><td></td></tr>
    <tr><td><code>media</code></td><td></td></tr>
    <tr><td><code>paint</code></td><td></td></tr>
    <tr><td><code>sdf</code></td><td></td></tr>
    <tr><td><strong>Navigation</strong></td><td><code>go</code></td><td></td></tr>
    <tr><td><strong>World model</strong></td><td><code>world</code></td><td></td></tr>
    <tr><td><strong>Signal declaration</strong></td><td><code>signal</code></td><td></td></tr>
    <tr><td><strong>Built-in value</strong></td><td><code>fit</code></td><td></td></tr>
  </tbody>
</table>
