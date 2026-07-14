<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>.nd Language Specification — Part 1: Grammar &amp; Lexical Structure</title>
    <style>
        :root {
            --bg: #ffffff;
            --fg: #1a1a2e;
            --muted: #555;
            --border: #d0d7de;
            --code-bg: #f6f8fa;
            --table-stripe: #f6f8fa;
            --link: #0969da;
            --heading: #0d1117;
            --accent-bg: #fdf6e3;
            --inline-code: #d73a49;
            --block-border: #d0d7de;
            --summary-bg: #f0f4f8;
            --tag-bg: #e8ecf1;
            --tag-fg: #1a1a2e;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Noto Sans', Helvetica, Arial, sans-serif;
            font-size: 16px;
            line-height: 1.6;
            color: var(--fg);
            background: var(--bg);
        }

        * {
            box-sizing: border-box;
        }

        body {
            max-width: 860px;
            margin: 0 auto;
            padding: 2rem 1.5rem 4rem;
        }

        h1,
        h2,
        h3,
        h4,
        h5,
        h6 {
            color: var(--heading);
            margin-top: 2em;
            margin-bottom: 0.5em;
            font-weight: 600;
            line-height: 1.3;
        }

        h1 {
            font-size: 2rem;
            border-bottom: 2px solid var(--border);
            padding-bottom: 0.4em;
            margin-top: 0;
        }

        h2 {
            font-size: 1.5rem;
            border-bottom: 1px solid var(--border);
            padding-bottom: 0.3em;
        }

        h3 {
            font-size: 1.2rem;
        }

        h4 {
            font-size: 1.05rem;
        }

        a {
            color: var(--link);
            text-decoration: none;
        }

        a:hover {
            text-decoration: underline;
        }

        p {
            margin: 0.75em 0;
        }

        blockquote {
            margin: 1em 0;
            padding: 0.5em 1em;
            border-left: 4px solid #d0d7de;
            background: #f9fafb;
            color: #57606a;
            border-radius: 0 4px 4px 0;
        }

        code {
            font-family: 'SF Mono', 'Fira Code', 'Fira Mono', 'JetBrains Mono', 'Consolas', 'Monaco', 'Courier New', monospace;
            font-size: 0.9em;
        }

        :not(pre)>code {
            background: #eff1f3;
            color: var(--inline-code);
            padding: 0.15em 0.4em;
            border-radius: 4px;
            white-space: nowrap;
        }

        pre {
            background: var(--code-bg);
            border: 1px solid var(--block-border);
            border-radius: 6px;
            padding: 1em 1.2em;
            overflow-x: auto;
            font-size: 0.88em;
            line-height: 1.5;
            margin: 1em 0;
        }

        pre code {
            background: none;
            color: var(--fg);
            padding: 0;
            white-space: pre;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin: 1.2em 0;
            font-size: 0.95em;
        }

        thead {
            background: #f0f2f5;
        }

        th,
        td {
            border: 1px solid var(--border);
            padding: 0.6em 0.9em;
            text-align: left;
            vertical-align: top;
        }

        th {
            font-weight: 600;
            color: var(--heading);
        }

        tbody tr:nth-child(even) {
            background: var(--table-stripe);
        }

        details {
            margin: 1em 0;
            border: 1px solid var(--border);
            border-radius: 6px;
            padding: 0.75em 1em;
            background: #fafbfc;
        }

        details[open] {
            background: #ffffff;
        }

        summary {
            font-weight: 600;
            cursor: pointer;
            padding: 0.25em 0;
            color: var(--heading);
            user-select: none;
        }

        summary:hover {
            color: var(--link);
        }

        details pre {
            margin-top: 0.75em;
            background: #f6f8fa;
        }

        hr {
            border: none;
            border-top: 1px solid var(--border);
            margin: 2em 0;
        }

        .tag {
            display: inline-block;
            background: var(--tag-bg);
            color: var(--tag-fg);
            font-size: 0.8em;
            font-weight: 600;
            padding: 0.15em 0.55em;
            border-radius: 999px;
            margin-right: 0.4em;
            vertical-align: middle;
        }

        .note {
            background: #fff8e7;
            border-left: 4px solid #f0c040;
            padding: 0.6em 1em;
            border-radius: 0 4px 4px 0;
            margin: 1em 0;
            font-size: 0.94em;
        }

        .toc {
            background: #f9fafb;
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 1em 1.3em;
            margin: 1.5em 0;
            list-style: none;
        }

        .toc summary {
            font-size: 1.05rem;
            font-weight: 700;
            margin-bottom: 0.5em;
        }

        .toc ul {
            margin: 0.5em 0 0;
            padding-left: 1.4em;
        }

        .toc li {
            margin: 0.3em 0;
        }

        .toc a {
            color: var(--link);
        }

        @media (max-width: 600px) {
            body {
                padding: 1rem 0.8rem 2rem;
            }

            pre {
                font-size: 0.8em;
                padding: 0.75em;
            }

            table {
                font-size: 0.82em;
            }
        }
    </style>
</head>
<body>

    <h1>.nd Language Specification — Part 1: Grammar &amp; Lexical Structure</h1>

    <details class="toc">
        <summary><strong>Table of Contents</strong></summary>
        <ul>
            <li><a href="#sec0">0. Conformance</a></li>
            <li><a href="#sec1">1. Source Form</a>
                <ul>
                    <li><a href="#sec1-1">1.1 Encoding</a></li>
                    <li><a href="#sec1-2">1.2 Whitespace</a></li>
                    <li><a href="#sec1-3">1.3 Line Endings</a></li>
                    <li><a href="#sec1-4">1.4 Comments</a></li>
                </ul>
            </li>
            <li><a href="#sec2">2. Lexical Grammar</a>
                <ul>
                    <li><a href="#sec2-1">2.1 Token Categories</a></li>
                    <li><a href="#sec2-2">2.2 Identifiers</a></li>
                    <li><a href="#sec2-3">2.3 Reserved Words</a></li>
                    <li><a href="#sec2-4">2.4 Numeric Literals</a></li>
                    <li><a href="#sec2-5">2.5 Hexadecimal Literals</a></li>
                    <li><a href="#sec2-6">2.6 String Literals</a>
                        <ul>
                            <li><a href="#sec2-6-1">2.6.1 Escape Sequences</a></li>
                            <li><a href="#sec2-6-2">2.6.2 Interpolation</a></li>
                        </ul>
                    </li>
                    <li><a href="#sec2-7">2.7 Array &amp; Map Literal Delimiters</a></li>
                    <li><a href="#sec2-8">2.8 Operators &amp; Punctuation</a></li>
                    <li><a href="#sec2-9">2.9 Indentation (INDENT/DEDENT token rule)</a></li>
                </ul>
            </li>
            <li><a href="#sec3">3. Syntactic Grammar</a>
                <ul>
                    <li><a href="#sec3-1">3.1 Program Structure</a></li>
                    <li><a href="#sec3-2">3.2 Node Declaration</a></li>
                    <li><a href="#sec3-3">3.3 Property Line</a></li>
                    <li><a href="#sec3-4">3.4 Content Verbs</a></li>
                    <li><a href="#sec3-5">3.5 Expressions</a></li>
                    <li><a href="#sec3-6">3.6 Control Constructs</a></li>
                    <li><a href="#sec3-7">3.7 Declarations</a></li>
                    <li><a href="#sec3-8">3.8 Component Definitions</a></li>
                    <li><a href="#sec3-9">3.9 World Declarations</a></li>
                </ul>
            </li>
            <li><a href="#sec4">4. Disambiguation Rules</a></li>
            <li><a href="#appendix-a">Appendix A. Full EBNF Grammar (consolidated)</a></li>
            <li><a href="#appendix-b">Appendix B. Reserved Word List</a></li>
        </ul>
    </details>

    <!-- ────────────────────────────────────────────── -->
    <!-- SECTION 0                                      -->
    <!-- ────────────────────────────────────────────── -->
    <h2 id="sec0">0. Conformance</h2>

    <blockquote>
        Don't ask questions whose answers are obvious from the design.
    </blockquote>

    <p>This document defines the definitive lexical and syntactic grammar for <code>.nd</code> source text.</p>

    <p>To implement a full compiler or interpreter pipeline, this specification must be paired with its companion
        documents:</p>
    <ul>
        <li><strong>Static Semantics:</strong> Governs type checking, name binding, and scope resolution. (see: <a
                href="#">Static Semantics specification</a>)</li>
        <li><strong>Runtime Semantics:</strong> Governs bytecode generation, frame execution, and standard library
            behavior. (see: <a href="#">Runtime Semantics specification</a>)</li>
    </ul>

    <p>Syntax verification occurs entirely before static-semantic analysis. The compiler must abort with a syntax error
        upon encountering any token sequence not defined by this grammar.</p>

    <h3>Notation</h3>
    <p>Grammar productions in this document use the EBNF notation defined by the <a
            href="https://www.w3.org/TR/xml/#sec-notation">W3C XML 1.0 (Fifth Edition) specification, §6</a>.</p>

    <hr>

    <!-- ────────────────────────────────────────────── -->
    <!-- SECTION 1                                      -->
    <!-- ────────────────────────────────────────────── -->
    <h2 id="sec1">1. Source Form</h2>

    <h3 id="sec1-1">1.1 Encoding</h3>
    <p><code>.nd</code> source files are UTF-8 encoded.</p>
    <p>If a source file contains invalid UTF-8 byte sequences, the compiler must reject the file immediately with a
        source-encoding error and abort before tokenization begins.</p>

    <h3 id="sec1-2">1.2 Whitespace</h3>
    <ul>
        <li>The space character (<code>U+0020</code>) is the only significant whitespace character for token separation
            within a line.</li>
        <li>Leading whitespace determines indentation level.</li>
        <li>The tab character (<code>U+0009</code>) is <strong>not permitted</strong> in leading whitespace.</li>
        <li>A line beginning with a tab is a lexical error.</li>
        <li>Tabs may only appear inside string literals.</li>
    </ul>

    <h3 id="sec1-3">1.3 Line Endings</h3>
    <p>The following terminators are supported:</p>
    <ul>
        <li>LF (<code>\n</code>)</li>
        <li>CRLF (<code>\r\n</code>)</li>
    </ul>
    <p>They are normalized to a single <code>\n</code> during tokenization. A lone CR (<code>\r</code>) not followed by
        LF is a lexical error.</p>

    <h3 id="sec1-4">1.4 Comments</h3>

    <pre><code>Comment ::= '//' [^\n]*</code></pre>

    <p>A line comment begins with <code>//</code> and continues until (but does not include) the line terminator.</p>
    <p>Discard everything from <code>//</code> up to the line terminator during tokenization.</p>
    <p>Comments are <strong>not</strong> recognized inside string literals. <code>//</code> inside a string is treated as
        literal characters.</p>

    <details>
        <summary>Example: Comments</summary>
        <pre><code>// through goes Hamilton!!!
            // Comments can be single line or multiline
            // and multiline is just multiple singleline
            @button
            text "Click me"                  // click him
            text "http://www.github.com"     // not a comment</code></pre>
    </details>

    <hr>

    <!-- ────────────────────────────────────────────── -->
    <!-- SECTION 2                                      -->
    <!-- ────────────────────────────────────────────── -->
    <h2 id="sec2">2. Lexical Grammar</h2>

    <h3 id="sec2-1">2.1 Token Categories</h3>
    <p>The lexer produces a stream of tokens in the following categories. Comments (§1.4) and non-indentation whitespace
        are consumed by the lexer and do not appear in the token stream.</p>

    <table>
        <thead>
            <tr>
                <th>Category</th>
                <th>Examples</th>
                <th>Defined in</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>Identifier</td>
                <td><code>count</code>, <code>post</code>, <code>card</code></td>
                <td>§2.2</td>
            </tr>
            <tr>
                <td>Node Sigil</td>
                <td><code>@</code></td>
                <td>§2.2</td>
            </tr>
            <tr>
                <td>Reserved Word</td>
                <td><code>var</code>, <code>if</code>, <code>each</code>, <code>import</code></td>
                <td>§2.3</td>
            </tr>
            <tr>
                <td>Integer</td>
                <td><code>200</code>, <code>-32</code>, <code>0</code></td>
                <td>§2.4</td>
            </tr>
            <tr>
                <td>Float</td>
                <td><code>0.08</code>, <code>-0.1</code></td>
                <td>§2.4</td>
            </tr>
            <tr>
                <td>Color</td>
                <td><code>0x0055ffff</code></td>
                <td>§2.5</td>
            </tr>
            <tr>
                <td>String</td>
                <td><code>"hello"</code>, multi-line, interpolated</td>
                <td>§2.6</td>
            </tr>
            <tr>
                <td>Punctuation</td>
                <td><code>(</code>, <code>)</code>, <code>[</code>, <code>]</code>, <code>{</code>, <code>}</code>,
                    <code>.</code>, <code>,</code></td>
                <td>§2.7, §2.8</td>
            </tr>
            <tr>
                <td>Operator</td>
                <td><code>+</code>, <code>==</code>, <code>?</code>, <code>??</code>, <code>|</code>, <code>..</code>
                </td>
                <td>§2.8</td>
            </tr>
            <tr>
                <td>INDENT / DEDENT</td>
                <td>(synthetic, no source text)</td>
                <td>§2.9</td>
            </tr>
            <tr>
                <td>NEWLINE</td>
                <td>(synthetic, statement separator)</td>
                <td>§2.9</td>
            </tr>
            <tr>
                <td>EOF</td>
                <td>(synthetic, end of token stream)</td>
                <td>—</td>
            </tr>
        </tbody>
    </table>

    <h3 id="sec2-2">2.2 Identifiers</h3>

    <pre><code>Identifier     ::= IdentStart IdentContinue*
IdentStart     ::= [a-zA-Z]
IdentContinue  ::= [a-zA-Z0-9_]</code></pre>

    <p>An identifier is a sequence of letters, digits, and underscores, not beginning with a digit or underscore.
        Identifiers are case-sensitive.</p>
    <p>The <code>@</code> sigil (<code>U+0040</code>) is a separate token (§2.1) and is not part of an identifier.
        <code>@name</code> is a grammar-level combination (§3.2), not a single lexical token.</p>
    <p>An identifier matching a reserved word (§2.3) is tokenized as the reserved word, not as
        <code>Identifier</code>.</p>

    <details>
        <summary>Example</summary>
        <pre><code>@card
            @big_number_two_eventy_wan_k
            @abstain_island</code></pre>
        <p>Here <code>card</code>, <code>big_number_two_eventy_wan_k</code>, and <code>abstain_island</code> are each
            tokenized as <code>Identifier</code>, preceded by a separate <code>@</code> token.</p>
    </details>

    <h3 id="sec2-3">2.3 Reserved Words</h3>
    <p>Reserved words cannot be used as <code>Identifier</code> (§2.2). A token matching a reserved word is classified as
        <code>ReservedWord</code>, not <code>Identifier</code>, regardless of position.</p>
    <p>See: <a href="#appendix-b">Appendix B: Reserved Word List</a>.</p>

    <h3 id="sec2-4">2.4 Numeric Literals</h3>

    <pre><code>NumericLiteral ::= FloatLiteral | IntLiteral
IntLiteral     ::= [0-9]+
FloatLiteral   ::= [0-9]+ '.' [0-9]+</code></pre>

    <p>The lexer classifies numbers as <strong>positive magnitudes</strong>.</p>
    <p>Negative values are produced <em>syntactically</em> when a <code>NumericLiteral</code> is preceded by a unary
        negation operator <code>-</code>, which is resolved and folded into a signed binary value during <a href="#">static
            analysis</a>, <em>not</em> during lexing or parsing.</p>
    <p>The presence of a fractional period <code>.</code> explicitly designates a <code>FloatLiteral</code>.</p>

    <details>
        <summary>Example: Tokenization of Numeric Literals</summary>
        <pre><code>var x 12          // IntLiteral(12)
            var y -12         // Operator(-), IntLiteral(12)
            var z 0.08        // FloatLiteral(0.08)</code></pre>
    </details>

    <h3 id="sec2-5">2.5 Hexadecimal Literals</h3>

    <pre><code>HexLiteral   ::= '0x' HexDigit{8}
HexDigit     ::= [0-9a-fA-F]</code></pre>

    <p>A <code>HexLiteral</code> represents an unsigned 32-bit integer value expressed as a base-16 magnitude. It begins
        with <code>0x</code> followed by exactly eight hexadecimal digits.</p>
    <p>Because <code>HexLiteral</code> shares its leading digit <code>0</code> with <code>IntLiteral</code> and
        <code>FloatLiteral</code> (§2.4), the lexer resolves this branch using standard LL(1) lookahead:</p>
    <ul>
        <li>On encountering <code>'0'</code>, it inspects the immediate next character.</li>
        <li>If the character is <code>'x'</code> or <code>'X'</code>, it commits to <code>HexLiteral</code> and consumes
            exactly eight hexadecimal digits.</li>
        <li>Any other character leaves the token in the <code>IntLiteral</code> or <code>FloatLiteral</code> pipeline.
        </li>
    </ul>
    <p>Hexadecimal letters are case-insensitive: <code>0x0055FFFF</code> and <code>0x0055ffff</code> are the same
        literal.</p>

    <details>
        <summary>Example: Tokenization of Hexadecimal Literals</summary>
        <pre><code>var mask 0x0055ffff   // HexLiteral(0x0055ffff), strict 8 digits, 32-bit value
            paint 0x0055FFFF      // HexLiteral(0x0055ffff), resolved as a color value by the 'paint' content verb.</code></pre>
    </details>

    <!-- ──────────────────────────── -->
    <!-- 2.6 String Literals          -->
    <!-- ──────────────────────────── -->
    <h3 id="sec2-6">2.6 String Literals</h3>

    <pre><code>StringLiteral ::= '"' RawContent? '"'
RawContent    ::= StringChar+
StringChar    ::= [^"\\{}] | EscapeSequence | Interpolation</code></pre>

    <p>A string literal begins and ends with double quotes <code>"</code>.</p>
    <p>The lexer isolates the raw text stream and computes the normalized value using a three-step pass:</p>
    <ol>
        <li>A leading newline (<code>\n</code>) immediately following the opening <code>"</code> is discarded.</li>
        <li>A trailing newline (<code>\n</code>) immediately preceding the closing <code>"</code> is discarded.</li>
        <li>The least-indented, non-blank line establishes the reference indentation. This exact number of leading spaces
            is stripped uniformly from all lines. Blank lines are unaffected.</li>
    </ol>
    <p>This normalization pass executes vacuously as a no-op for single-line strings containing no internal newlines.</p>

    <h4>Lexical Values</h4>
    <p>A generated <code>StringLiteral</code> token preserves both the unmodified literal source text
        (<code>raw_value</code>) and the post-normalization content (<code>clean_value</code>).</p>

    <h4>Scope of Markdown Syntax</h4>
    <p>A string literal may contain Markdown formatting (such as <code>*emphasis*</code> or <code>`verbatim`</code>). The
        lexer completely ignores this formatting, treating and preserving all Markdown syntax patterns as ordinary,
        <em>uninterpreted</em> characters.</p>

    <details>
        <summary>Example: String Literals</summary>
        <pre><code>text "Hello."
            // clean_value: "Hello."

            text "
            First `paragraph`.

            Second paragraph, **still here.**
            "
            // clean_value: "First `paragraph`.\n\nSecond paragraph, **still here.**"</code></pre>
    </details>

    <!-- ──────────────────────────── -->
    <!-- 2.6.1 Escape Sequences       -->
    <!-- ──────────────────────────── -->
    <h4 id="sec2-6-1">2.6.1 Escape Sequences</h4>

    <pre><code>EscapeSequence ::= '\' ('"' | '\' | 'n' | 't' | '{' | '}')</code></pre>

    <p>Upon encountering an unescaped backslash (<code>\</code>), the lexer consumes the immediate next character
        literally as <em>content</em>, bypassing string termination or interpolation triggers.</p>
    <p>Supported escape mappings include <code>\"</code>, <code>\\</code>, <code>\n</code>, <code>\t</code>,
        <code>\{</code>, and <code>\}</code>. Any backslash followed by a character outside this set is a lexical error.</p>

    <details>
        <summary>Examples: String Literals — Escape Sequences</summary>
        <pre><code>text "She said \"go\" and left."
            // clean_value: "She said "go" and left."

            text "Use \\n for a literal backslash-n."
            // clean_value: "Use \n for a literal backslash-n."

            text "Curly braces: \{not interpolated\}"
            // clean_value: "Curly braces: {not interpolated}"

            text "Invalid: \Alonso"
            // lexical error: 'A' is not a valid escape target</code></pre>
    </details>

    <!-- ──────────────────────────── -->
    <!-- 2.6.2 Interpolation          -->
    <!-- ──────────────────────────── -->
    <h4 id="sec2-6-2">2.6.2 Interpolation</h4>

    <pre><code>Interpolation ::= '{' Expression '}'</code></pre>

    <p>An unescaped <code>{</code> inside a string marks the beginning of an interpolation block. The enclosed text is
        evaluated as an Expression (§3.5). The matching unescaped <code>}</code> at the same nesting depth terminates the
        interpolation block, resuming string-content scanning.</p>
    <p>Because <code>StringLiteral</code> and <code>Expression</code> are mutually recursive productions, interpolation
        blocks may contain nested string literals or nested brace pairs (such as map literals) without interfering with
        the outer string's own structure.</p>

    <details>
        <summary>Examples: String Literals — String Interpolation</summary>
        <pre><code>text "Hello, {user.name}!"
            text "Result: {count > 10 ? "many" : "few"}"</code></pre>
        <p>The second example contains nested string literals (<code>"many"</code>, <code>"few"</code>) inside the
            interpolation block, which are tokenized independently of the outer string's bounding quotes.</p>
    </details>

    <h3 id="sec2-7">2.7 Array &amp; Map Literal Delimiters</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec2-8">2.8 Operators &amp; Punctuation</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec2-9">2.9 Indentation (INDENT/DEDENT token rule)</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <hr>

    <!-- ────────────────────────────────────────────── -->
    <!-- SECTION 3                                      -->
    <!-- ────────────────────────────────────────────── -->
    <h2 id="sec3">3. Syntactic Grammar</h2>

    <h3 id="sec3-1">3.1 Program Structure</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec3-2">3.2 Node Declaration</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec3-3">3.3 Property Line</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec3-4">3.4 Content Verbs</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec3-5">3.5 Expressions</h3>

    <h4>3.5.1 Precedence Table</h4>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h4>3.5.2 Operators</h4>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h4>3.5.3 Function Calls</h4>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h4>3.5.4 Lambda Form</h4>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec3-6">3.6 Control Constructs (<code>if</code>/<code>else</code>, <code>on</code>, <code>each</code>)</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec3-7">3.7 Declarations (<code>var</code>, <code>data</code>, <code>task</code>, <code>import</code>,
        <code>global</code>)</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec3-8">3.8 Component Definitions (parameters, <code>slot</code>)</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3 id="sec3-9">3.9 World Declarations</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <hr>

    <!-- ────────────────────────────────────────────── -->
    <!-- SECTION 4                                      -->
    <!-- ────────────────────────────────────────────── -->
    <h2 id="sec4">4. Disambiguation Rules</h2>

    <h3>4.1 Property vs. Child Node</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3>4.2 <code>each</code> Collection-form vs. Range-form</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <h3>4.3 [others as found]</h3>
    <p class="note"><em>Reserved for future specification.</em></p>

    <hr>

    <!-- ────────────────────────────────────────────── -->
    <!-- APPENDIX A                                     -->
    <!-- ────────────────────────────────────────────── -->
    <h2 id="appendix-a">Appendix A. Full EBNF Grammar (consolidated)</h2>
    <p class="note"><em>Reserved for future specification. Will contain the complete, consolidated EBNF grammar for all
            productions defined in this document.</em></p>

    <hr>

    <!-- ────────────────────────────────────────────── -->
    <!-- APPENDIX B                                     -->
    <!-- ────────────────────────────────────────────── -->
    <h2 id="appendix-b">Appendix B. Reserved Word List</h2>
    <p>The following tokens are unconditionally reserved in all <code>.nd</code> files:</p>

    <table>
        <thead>
            <tr>
                <th>Category</th>
                <th>Token</th>
                <th>Notes</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td rowspan="6"><strong>Control flow</strong></td>
                <td><code>if</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>else</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>on</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>each</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>stop</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>return</code></td>
                <td></td>
            </tr>
            <tr>
                <td rowspan="3"><strong>State</strong></td>
                <td><code>var</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>data</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>task</code></td>
                <td></td>
            </tr>
            <tr>
                <td rowspan="3"><strong>Structure</strong></td>
                <td><code>import</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>global</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>slot</code></td>
                <td></td>
            </tr>
            <tr>
                <td rowspan="4"><strong>Operators</strong></td>
                <td><code>and</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>or</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>not</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>as</code></td>
                <td></td>
            </tr>
            <tr>
                <td rowspan="3"><strong>Literals</strong></td>
                <td><code>true</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>false</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>null</code></td>
                <td></td>
            </tr>
            <tr>
                <td rowspan="7"><strong>Properties</strong></td>
                <td><code>pos</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>size</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>type</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>radius</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>font</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>surface</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>scroll</code></td>
                <td></td>
            </tr>
            <tr>
                <td rowspan="4"><strong>Content verbs</strong></td>
                <td><code>text</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>media</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>paint</code></td>
                <td></td>
            </tr>
            <tr>
                <td><code>sdf</code></td>
                <td></td>
            </tr>
            <tr>
                <td><strong>Navigation</strong></td>
                <td><code>go</code></td>
                <td></td>
            </tr>
            <tr>
                <td><strong>World model</strong></td>
                <td><code>world</code></td>
                <td></td>
            </tr>
            <tr>
                <td><strong>Signal declaration</strong></td>
                <td><code>signal</code></td>
                <td></td>
            </tr>
            <tr>
                <td><strong>Built-in value</strong></td>
                <td><code>fit</code></td>
                <td></td>
            </tr>
        </tbody>
    </table>

    <hr>
    <p style="text-align:center;color:#888;font-size:0.85em;margin-top:2em;">
        &copy; .nd Language Specification — Part 1: Grammar &amp; Lexical Structure
    </p>

</body>
</html>