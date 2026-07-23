# Canonical Source Language (CSL)

## 0. Purpose

The Canonical Source Language (CSL) is the canonical lexical representation of a `.nd` source file.

CSL is not intended to be written by humans. It is produced by a lexer and consumed by a parser.

Every conforming lexer, regardless of the human source syntax it accepts, shall produce an equivalent CSL for semantically equivalent source programs.

---

## 1. Structure

A CSL document is an ordered sequence of tokens.

```ebnf
CSL ::= Token*
```

The order of tokens is significant.

---

## 2. Token

A token is the fundamental unit of CSL.

Every token consists of:

- exactly one Kind;
- zero or more Payloads.

```ebnf
Token ::= Kind Payload*
```

---

## 3. Kind

The Kind identifies the canonical meaning of a token.

Every token has exactly one Kind.

The complete set of valid kinds is defined by the language specification.

Examples include:

- Identifier
- ReservedWord
- IntLiteral
- FloatLiteral
- StringLiteral
- PlusOp
- LeftParen
- NEWLINE
- INDENT
- DEDENT
- EOF

---

## 4. Payload

A payload stores information that distinguishes one token from another token of the same Kind.

A token may contain zero or more payloads.

Examples:

- Identifier → name
- IntLiteral → value
- FloatLiteral → value
- StringLiteral → text

Tokens whose meaning is completely determined by their Kind have no payloads.

Examples:

- PlusOp
- LeftParen
- NEWLINE
- EOF

---

## 5. Metadata

Metadata is not part of CSL.

Information such as source location, file name, line number, column number, diagnostics, implementation state, or other bookkeeping data is outside the definition of CSL.

Implementations may retain such information internally, but it is not visible to the parser through CSL.