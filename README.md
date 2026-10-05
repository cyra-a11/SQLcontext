# SqlContext

**SQL injection input analyzer for learning and understanding SQLi syntax.**

SQLContext is a lightweight Python CLI that analyzes SQL-injection-style input and explains what it detects.

It can identify common SQL syntax elements, estimate the likely input context, evaluate simple boolean expressions, identify common SQLi patterns, and suggest likely corrections for malformed input.

> **SQLContext is an educational analyzer. It does not scan websites, send HTTP requests, exploit applications, or determine whether a real application is vulnerable.**

## Features

* Detect SQL quotes
* Detect SQL comment syntax
* Detect boolean expressions
* Evaluate simple TRUE/FALSE comparisons
* Estimate numeric vs. string context
* Detect common SQLi patterns
* Explain the conceptual transformation
* Detect common syntax mistakes
* Suggest likely corrections
* Run entirely from the command line
* No external dependencies

## Example

```bash
python sqlcontext.py "'OR 1==1-"
```

Output:

```text
SQLContext
────────────────────────────────────────

Input:
  'OR 1==1-

Context:
  LIKELY STRING

Detected:
  ✓ Quotes: single quote
  ✓ Boolean expression detected

Syntax review:
  ! `==` is likely incorrect SQL equality syntax.
  ! A trailing single `-` is not a complete `--` line comment.

Likely correction:
  ' OR 1=1--
```

Another example:

```bash
python sqlcontext.py "' AND 1=2--"
```

The analyzer identifies the boolean expression and evaluates it as `FALSE`.

## Installation

Clone the repository:

```bash
git clone https://github.com/cyra-a11/sqlcontext.git
cd sqlcontext
```

No external Python packages are required.

## Usage

```bash
python sqlcontext.py "<input>"
```

Examples:

```bash
python sqlcontext.py "' OR 1=1--"
```

```bash
python sqlcontext.py "1 OR 1=1"
```

```bash
python sqlcontext.py "' AND 1=2--"
```

```bash
python sqlcontext.py "' UNION SELECT NULL--"
```

```bash
python sqlcontext.py "'OR 1==1-"
```

## How it works

SQLContext processes the supplied input through several stages:

```text
Input
  ↓
Pattern detection
  ↓
Context analysis
  ↓
Boolean evaluation
  ↓
Syntax validation
  ↓
Correction
  ↓
Explanation
```

### 1. Pattern detection

The analyzer looks for elements such as:

* quotes
* SQL comments
* logical operators
* comparisons
* SQL keywords
* UNION SELECT patterns

### 2. Context analysis

The tool estimates whether the input is more consistent with:

```text
NUMERIC
STRING
LIKELY NUMERIC
LIKELY STRING
UNKNOWN
```

This is only an inference from the supplied string. The actual SQL context cannot be known without seeing the application's query.

### 3. Boolean evaluation

Simple comparisons such as:

```text
1=1
1=2
'a'='a'
'a'='b'
```

can be evaluated by the tool.

### 4. Syntax correction

If the input contains a likely mistake, SQLContext keeps the original input unchanged and shows a possible correction.

For example:

```text
'OR 1==1-
```

may be reported as:

```text
' OR 1=1--
```

The correction is a **pattern-based suggestion**, not a guarantee of database-specific validity.

## Limitations

SQL syntax differs between database engines and query contexts.

Therefore, SQLContext uses terms such as:

* likely
* possible
* common
* inferred

rather than claiming that a particular interpretation is always correct.

Most importantly:

**SQLContext cannot determine whether an application is actually vulnerable to SQL injection.**

It analyzes an input string only.

## Safety

This project is intended for cybersecurity education and authorized security research.

It does not:

* scan websites
* send requests
* exploit applications
* extract database contents
* bypass authentication
* enumerate databases
* perform automated attacks

## Project structure

```text
sqlcontext/
├── sqlcontext.py       # CLI entry point
├── analyzer.py         # Main analysis and report generation
├── patterns.py         # SQL pattern detection
├── evaluator.py        # Simple expression evaluation
├── explainer.py        # Context and explanation logic
├── tests/
│   └── test_analyzer.py
├── README.md
├── LICENSE
└── .gitignore
```

## Testing

If `pytest` is installed:

```bash
pytest
```

The test suite covers:

* boolean TRUE detection
* boolean FALSE detection
* syntax correction
* UNION detection
* unknown input handling

## Future improvements

Possible future improvements include:

* database-specific syntax profiles
* more SQL dialect recognition
* improved expression parsing
* richer explanations
* additional unit tests
* structured JSON output

These features are intentionally outside the initial scope.

## License

MIT License.
