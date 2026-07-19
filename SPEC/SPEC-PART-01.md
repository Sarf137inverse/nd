# .nd Language Specification - Part 1: Grammar & Lexical Structure  

## 0. Conformance

> Don't ask questions whose answers are obvious from the design

This document defines the definitive lexical and syntactic grammar for .nd source text.

​To implement a full compiler or interpreter pipeline, this specification must be paired with its companion documents:
- ​**Static Semantics:** Governs type checking, name binding, and scope resolution. (see: [Static Semantics specification]())
- **​Runtime Semantics:** Governs bytecode generation, frame execution, and standard library behavior. (see: [Runtime Semantics specification]())

​Syntax verification occurs entirely before static-semantic analysis. If a source file contains an invalid UTF-8 byte sequence, the compiler shall reject the source file before tokenization begins.

### Notation  
Grammar productions in this document use the EBNF notation defined
by the [W3C XML 1.0 (Fifth Edition) specification, §6](https://www.w3.org/TR/xml/#sec-notation).


## 1. Source Form 

### 1.1 Encoding

'.nd' source files are UTF-8 encoded. 

If a source file contains invalid UTF-8 byte sequences, the compiler shall reject the source file immediately with a source-encoding error and abort before tokenization begins.

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

| Category       | Examples                              | Defined in |
|----------------|---------------------------------------|------------|
| Identifier     | `count`, `post`, `card`               | §2.2       |
| Wildcard       | `_`                                   | §2.3       |
| ReservedWord   | `var`, `if`, `each`                   | §2.5       |
| Literal        | `12`, `0.8`, `0x44bb66aa`, `"hello"`  | §2.6       |
| NodeSigil      | `@`                                   | §2.10      |
| Operator       | `+`, `==`, `?`, `..`                  | §2.9       |
| Punctuation    | `(`, `)`, `[`, `]`, `{`, `}`          | §2.10      |
| NEWLINE        | (synthetic)                           | §2.11      |
| INDENT         | (synthetic)                           | §2.12      |
| DEDENT         | (synthetic)                           | §2.12      |
| EOF            | (synthetic)                           | §2.11      |

### 2.2 Identifiers

```ebnf
Identifier ::= IdentStart IdentContinue*
IdentStart ::= [a-zA-Z]
IdentContinue ::= [a-zA-Z0-9_]
```

An identifier is a sequence of letters, digits, and underscores, beginning only with a letter. Identifiers are case-sensitive.

The `@` sigil (U+0040) is a separate token (§2.10) and is not part
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

### 2.5 Reserved Words

Reserved words cannot be used as `Identifier` (§2.2). A token
matching a reserved word is classified as `ReservedWord`, not
`Identifier`, regardless of position.

See: [Appendix B: Reserved Word List](#appendixbreservedwordlist).

### 2.6 Literals  

#### 2.6.1 Integer Literals & Float Literals

```ebnf
IntLiteral     ::= [0-9]+
FloatLiteral   ::= [0-9]+ '.' [0-9]+
```

The lexer classifies numbers as positive magnitudes.
 
A leading `-` is tokenized separately as MinusOp. Signed numeric values are formed during syntactic and semantic analysis, not during lexing.
 
The presence of a fractional period `.` explicitly designates a FloatLiteral. 

<details>

<summary>Example: Tokenization of Numeric Literals</summary>

```nd
var x 12          // IntLiteral(12)
var y -12         // Operator(-), IntLiteral(12)
var z 0.08        // FloatLiteral(0.08)
```
</details>

#### 2.6.2 Hexadecimal Literals

```ebnf
HexLiteral   ::= '0x' HexDigit{8}
HexDigit     ::= [0-9a-fA-F]
```

A HexLiteral represents an unsigned 32-bit integer value expressed as a base-16 magnitude. It begins with 0x followed by exactly eight hexadecimal digits.

Upon reading 0, if the following character is x, the lexer scans a hexadecimal literal; otherwise it scans a decimal literal.

Hexadecimal letters are case-insensitive.

<details>
<summary>Example: Tokenization of Hexadecimal Literals</summary>

```nd
var mask 0x0055ffff   // HexLiteral(0x0055ffff), strict 8 digits, 32-bit value
paint 0x0055ffff      // HexLiteral(0x0055ffff), resolved as a color value by the 'paint' content verb.
paint 0x0055FFFF      // `0x0055FFFF` and `0x0055ffff` are the same literal.
```

</details>

#### 2.6.3 String Literals

```ebnf
StringLiteral ::= '"' RawContent? '"'
RawContent    ::= StringChar+
StringChar    ::= [^"\\{}] | EscapeSequence | Interpolation
```

A string literal begins and ends with double quotes `"`. 

The lexical value of a multiline string is obtained by:
 1. A leading newline (\n) immediately following the opening `"` is discarded.
 2. A trailing newline (\n) immediately preceding the closing `"` is discarded.
 3. The least-indented, non-blank line establishes the reference indentation. This exact number of leading spaces is stripped uniformly from all lines. Blank lines are unaffected.

This normalization pass executes vacuously as a no-op for single-line strings containing no internal newlines.

​Markdown has no lexical significance inside string literals.

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

##### 2.6.3.1 Escape Sequences

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

##### 2.6.3.2 Interpolation

```ebnf
Interpolation ::= '{' Expression '}'
```

An unescaped `{` inside a string marks the beginning of an interpolation block. The enclosed text is evaluated as an Expression (§3.5). The matching unescaped `}` at the same nesting depth terminates the interpolation block, resuming string-content scanning.

Because StringLiteral and Expression are mutually recursive productions, interpolation blocks may contain nested string literals or nested brace pairs without interfering with the outer string's own structure.

<details>
<summary>Examples: String Literals - String Interpolation</summary>

```nd
text "Hello, {user.name}!"
text "Result: {count > 10 ? "many" : "few"}"
```
The second example contains nested string literals ("many", "few") inside the interpolation block, which are tokenized independently of the outer string's bounding quotes.
</details>

### 2.9 Operators

This section defines every operator token recognized by the lexer. Their syntactic roles, precedence, associativity, and evaluation semantics are defined elsewhere in this specification.

#### 2.9.1 Operator Tokens

```ebnf
PlusOp              ::= '+'
MinusOp             ::= '-'
StarOp              ::= '*'
SlashOp             ::= '/'
PercentOp           ::= '%'

AmpersandOp         ::= '&'
CaretOp             ::= '^'
TildeOp             ::= '~'

ShiftLeftOp         ::= '<<'
ShiftRightOp        ::= '>>'

EqualOp             ::= '='
PlusEqualOp         ::= '+='
MinusEqualOp        ::= '-='
StarEqualOp         ::= '*='
SlashEqualOp        ::= '/='
PercentEqualOp      ::= '%='
AmpersandEqualOp    ::= '&='
CaretEqualOp        ::= '^='
ShiftLeftEqualOp    ::= '<<='
ShiftRightEqualOp   ::= '>>='

EqualEqualOp        ::= '=='
BangEqualOp         ::= '!='
LessOp              ::= '<'
LessEqualOp         ::= '<='
GreaterOp           ::= '>'
GreaterEqualOp      ::= '>='

QuestionOp          ::= '?'
QuestionQuestionOp  ::= '??'

PipeOp              ::= '|'
RangeOp             ::= '..'
```

#### 2.9.2 Longest-Match Rule

The lexer applies the longest-match rule when recognizing operator tokens.

When a sequence of characters may form either a single operator token or multiple shorter tokens, the longest valid operator token shall be produced.

<details>
<summary>Examples: Longest-Match Rule</summary>

- `..` is tokenized as a single `RangeOp`.
- `??` is tokenized as a single `QuestionQuestionOp`.
- `==`, `!=`, `<=`, and `>=` are each tokenized as single operator tokens.
- `+=`, `<<=`, and other compound assignment forms are each tokenized as single operator tokens.

</details>

#### 2.9.3 Word Operators

The reserved words `and`, `or`, and `not` participate in expression grammar as logical operators.

Because they are reserved words (§2.5), they are tokenized as `ReservedWord` tokens rather than `Operator` tokens.

The reserved word `as` is not an operator token. It participates only in the grammar for binding expressions and is defined in the syntactic grammar.

<details>
<summary>Example: Tokenization of Operators</summary>

```nd
var x = a + b * c

if x >= 10
  text "large"

value ?? fallback

count += 1

0 .. 10
```

The corresponding operator tokens are:

```ebnf
EqualOp
PlusOp
StarOp
GreaterEqualOp
QuestionQuestionOp
PlusEqualOp
RangeOp
```

</details>

### 2.10 Punctuation

This section defines every punctuation token recognized by the lexer. Their syntactic roles are defined elsewhere in this specification.

#### 2.10.1 Punctuation Tokens

```ebnf
AtSign         ::= '@'

LeftParen      ::= '('
RightParen     ::= ')'

LeftBracket    ::= '['
RightBracket   ::= ']'

LeftBrace      ::= '{'
RightBrace     ::= '}'

Dot            ::= '.'
Comma          ::= ','
Colon          ::= ':'
Semicolon      ::= ';'
```

#### 2.10.2 Longest-Match Rule

Punctuation tokens are recognized only after the longest-match rule for operator tokens (§2.9.2) has been applied. 

eg: `..` is tokenized as `RangeOp`, not two `Dot` tokens.

<details>
<summary>Example: Tokenization of Punctuation</summary>

```nd
@button(size.x, colors[0]);
```

The corresponding punctuation tokens are:

```text
AtSign
LeftParen
Dot
Comma
LeftBracket
RightBracket
RightParen
Semicolon
```

</details>

  
<details>
<summary><b>Note: Recognition vs. Usage</b></summary>

Recognition of a punctuation token by the lexer does not imply that it is syntactically valid.

Some punctuation tokens are recognized for diagnostics, future language evolution, or implementation convenience, and may not participate in any grammar production defined by this specification.

The parser shall report a syntax error upon encountering a punctuation token in a context where no grammar production permits it.

</details>

### 2.11 Synthetic Tokens

### 2.12 Indentation (INDENT/DEDENT token rule)

## 3. Syntactic Grammar

### 3.1 Program Structure

### 3.2 Binding Targets

```ebnf
BindingTarget ::= Identifier | Wildcard
```

A BindingTarget is a syntactic abstraction representing any grammar position where a value may be introduced, either by binding it to an identifier or by discarding it with the wildcard.

### 3.3 Node Declarations

### 3.4 Property Lines

### 3.5 Content Verbs

### 3.6 Expressions

### 3.6.1 Precedence Table

### 3.6.2 Operators

### 3.6.3 Function Calls

### 3.6.4 Lambda Form

### 3.7 Collection Literals

#### 3.7.1 Array Literals

```ebnf
ArrayLiteral ::= '[' (Expression (' ' Expression)*)? ']'
```

An ArrayLiteral consists of zero or more Expressions enclosed in [ and ].

Adjacent elements are separated by whitespace. Commas are not permitted.

<details>
<summary>Example: Array Literals</summary>var tags ["work" "life" "art"]

</details>

### 3.8 Control Constructs

### 3.9 Declarations

### 3.10 Component Definitions

### 3.11 World Declarations

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
