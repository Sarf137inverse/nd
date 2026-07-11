# .nd Language Specification - Part 1: Grammar & Lexical Structure  

## 0. Conformance
This document specifies the lexical and syntactic grammar of `.nd`  

**source text:** what character sequences constitute valid `.nd` programs,
and how valid programs decompose into tokens and syntax trees.

This document does NOT specify:
- Type checking, scope resolution, or any other rule that requires
  more than syntactic analysis (see: [Static Semantics specification]())
- Runtime behavior, frame execution order, or `.ndb` binary format
  (see: [Runtime Semantics specification]())
- Standard library contents (`hid/*.ndh`, `sdf.ndh`, etc.) — these
  are ordinary `.nd` programs, governed entirely by this document
  and the Static/Runtime Semantics documents, with no special status

A conforming implementation MUST accept every program this grammar
defines as valid, and MUST reject every program it defines as invalid,
as a syntax error, before any static-semantic analysis occurs.

### Notation  
Grammar productions in this document use the EBNF notation defined
by the [W3C XML 1.0 (Fifth Edition) specification, §6](https://www.w3.org/TR/xml/#sec-notation).

Where this document's EBNF and any accompanying prose appear to disagree, the EBNF is authoritative. Prose describes intent; worked examples illustrate it. Neither is normative on its own.


## 1. Source Form 

### 1.1 Encoding
'.nd' source files are UTF-8 encoded. A file containing invalid UTF-8 byte sequences shall be rejected before tokenization. This is a source-encoding error (distinct from syntax error) and must be reported as such.

### 1.2 Whitespace
- The space character (U+0020) is the only significant whitespace character for token seperation within a line.  
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
Erase everything from `//` up to the line terminator during tokenization.

Comments are not recognized inside string literals. // inside a string is treated as literal characters.

<details>
<summary>Example: Comments</summary>

```nd
// through goes Hamilton!!!
// Comments can be single line or multiline
// and multiline is just multiple singleline
@button
  text "Click me"   // click him

```

</details>

## 2. Lexical Grammar

### 2.1 Token Categories

The lexer produces a stream of tokens in the following categories. Comments (§1.4) and non-indentation whitespace are consumed by the lexer and do not appear in the token stream.

| Category | Examples | Defined in |
|----------|----------|------------|
| Identifier | `count`, `post`, `card` | §2.2 |
| Reserved Word | `var`, `if`, `each`, `import` | §2.3 |
| Integer | `200`, `-32`, `0` | §2.4 |
| Float | `0.08`, `-0.1` | §2.4 |
| Color | `0x0055ffff` | §2.5 |
| String | `"hello"`, multi-line, interpolated | §2.6 |
| Punctuation | `(`, `)`, `[`, `]`, `{`, `}`, `.`, `,` | §2.7, §2.8 |
| Operator | `+`, `==`, `?`, `??`, `\|`, `..` | §2.8 |
| Node Sigil | `@` | §2.2 |
| INDENT / DEDENT | (synthetic, no source text) | §2.9 |
| NEWLINE | (synthetic, statement separator) | §2.9 |
| EOF | (synthetic, end of token stream) | — |

### 2.2 Identifiers

```ebnf
Identifier ::= IdentStart IdentContinue*
IdentStart ::= [a-zA-Z]
IdentContinue ::= [a-zA-Z0-9_]
```

An identifier is a sequence of letters, digits, and underscores,
not beginning with a digit or underscore. Identifiers are case-sensitive.

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

Here `count`, `post_card`, and `x1` are each tokenized as
`Identifier`, preceded by a separate `@` token.
</details>

### 2.3 Reserved Words

### 2.4 Numeric Literals (int, float, norm-context note)

### 2.5 Color Literals

### 2.6 String Literals

#### 2.6.1 Single-line

#### 2.6.2 Multi-line (capture + normalization rule)

#### 2.6.3 Escape Sequences

#### 2.6.4 Interpolation

### 2.7 Array & Map Literal Delimiters

### 2.8 Operators & Punctuation

### 2.9 Indentation (INDENT/DEDENT token rule)

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

