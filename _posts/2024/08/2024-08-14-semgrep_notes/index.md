---
layout: post
title: Semgrep Notes
---

# Semgrep Notes Intro
These are some of my Semgrep notes gathered while going through the tutorial as well as running in operations for future reference.
  
## Semgrep
Semgrep is a powerful static analysis tool designed to scan code for patterns that may indicate potential vulnerabilities. 
- It's fairly easy to use and get up to speed with. 
- It’s an excellent tool to integrate into your SAST process for AppSec engagements.
  
  
---   
## Rule Syntax
Docs: https://semgrep.dev/docs/writing-rules/overview
> [!INFO] SemGrep uses YAML syntax.
> The pipe (`|`) after `pattern-inside` is YAML syntax that permits multi-line strings.
  
### ellipsis operator (**...**)
#### - Skip over
Allows you to skip over stuff you don't care about, similar to **`*`** star in RegEx.
```python
func(get_user(...) + "is my userid")
```
- Example code match:
```python
func(get_user(1) + "is my userid")
```
  
#### - Constant strings
Inside quotation marks, **`"..."`** , Semgrep matches any constant string.
```python
# Rule
func(arg, "...")
```
- Example code match:
```python
# Match
func(arg, "my string")
```
  
#### - Ignore in-between code
It can be used to ignore in between code (expressions or statements). Example:
```python
function_A(param)
...
function_Z(param)
```
  
#### - Ordered lists
The ellipses can be used to match a pattern in an ordered list such as method/function arguments.
- It can be used to find the first expression is a list like: `open("r", ...)`
- It can be used to find the last expression is a list like: `open(..., "r")`
- It can be used to find an expression anywhere in a list by using ellipses on both sided like: `open(..., "r", ...)`
  
  
### Metavariables
Metavariables allow you to match something in case you don't know exactly what to match. Similar to capture groups in RegEx.
- Must start with **$** dollar sign
- Can only contain:
	- Uppercase chars
	- Digits
	- Underscores
  
#### Metavariables Matching a function
Metavariables can be used to match a function:
- In this example "`$FUNC`" will match any function name.
```python
def $FUNC(...):
	...
```
- In this example it will match any function "`$FUNC`", in which it's body is making a function call to requests.
```python
def $FUNC(...):
    ...
    requests.$METHOD(...)
```
  
#### Metavariables matching regular variables
Metavariables can be used to match regular variables as well.
- Example of catching a bug where the file is open for read and attempting to write to it:
```python
$FD = open($FILENAME, 'r', ...)
...
$FD.write(...)
```
  
### Metavariables vs Ellipses
> [!QUOTE] Using a metavariable tells Semgrep, "something is here, but I don't know what it is." Using an ellipsis tells Semgrep, "I don't care what is between here and there."
  
  
---   
## 🔥 Tip for Starting a Rule

If you are having trouble matching a pattern starting from scratch, simply: 
1. Copy and paste the original code as a pattern
2. Generalize pattern by using ellipses for non-important lines and where other code could be replaced
3. Replace variables with metavariables.
  
  
---    
## Pattern Composition
>[!IMPORTANT] [Pattern-Examples](https://semgrep.dev/docs/writing-rules/pattern-examples/)
  
### 1. Either / Or  ([SemGrep Tutorial](https://semgrep.dev/learn/composition/1))
If you want to match _either_ pattern1 OR pattern2, use `pattern-either`.
```yaml
rules:
  - id: use-string-equals
    message: In Java, do not use == with strings. Use String.equals() instead.
    pattern-either:
      - pattern: if ($X == "...") ...
      - pattern: if ("..." == $X) ...
```
#### Example Code that matches this rule:
```csharp
public class Example {
  public int foo(String a, int b) {
      if (a == "hello") return 1;
      // Match here too by adding another pattern clause.
      if ("hello" == a) return 2;
      // Do not match here
      print("hello")
  }
}
```
  
### 2. Pattern is NOT ([SemGrep Tutorial](https://semgrep.dev/learn/composition/2))
You can use **`pattern-not`** to filter out patterns you do not want to match.
```yaml
rules:
  - id: subprocess-call
    patterns:
    - pattern: subprocess.call(...)
    # This says never match if first argument is a string
    - pattern-not: subprocess.call("...", ...)
```
#### Example Code that matches this rule:
```python
import subprocess

subprocess.call("ls -a .")  # Try not to match here.

dir = "/tmp"
subprocess.call("ls -a " + dir) # or here

subprocess.call(dir, shell=True) # or here!

subprocess.call(nonstring) # MATCH THIS

subprocess.call(nonstring, shell=True) # and this!
```
  
### 3. Pattern is inside - [SemGrep Tutorial](https://semgrep.dev/learn/composition/3)
As the name implies, **`pattern-inside`** lets you search for patterns **inside** the pattern specified by `pattern-inside`.
```yaml
rules:
  - id: Go_http-responsewriter-write
    patterns:
    - pattern-inside: |
        func $FUNC(...) {
          ...
        }
    - pattern: $WRITER.Write(...)
```
#### Example Go Code that matches this rule:
```go
package main

import (
  "bytes"
  "fmt"
  "log"
  "net/http"
)

func writeMessage(msg string, buf *bytes.Buffer) {
  // Try not to match here by using pattern-inside.
  buf.Write([]byte(msg))
}

func errorPage(hw http.ResponseWriter, r *http.Request) {
  hw.WriteHeader(http.StatusAccepted)
  hw.Write([]byte("Error!"))
}

func indexPage(w http.ResponseWriter, r *http.Request) {
  const template = `
  <html>
  <body>
    <h1>Greetings!</h1>
    <h2>%s</h2>
  </body>
  </html>`

  w.WriteHeader(http.StatusAccepted)
  w.Write([]byte(fmt.Sprintf(template, "Semgrep!")))
}

func main() {
  http.HandleFunc("/", indexPage)
  http.HandleFunc("/error", errorPage)
  http.ListenAndServe(":8080", nil)
}
```
  
### 4. Pattern is NOT inside [SemGrep Tutorial](https://semgrep.dev/learn/composition/4)
A **`pattern-not-inside`** **filters out** any matches inside the **range** defined by the pattern.
> [!NOTE] Both of these rules below match the pattern
#### Rule 1
```yaml
rules:
  - id: secure-flag-not-set
    patterns:
    - pattern: $RESPONSE.addCookie($COOKIE);
    - pattern-not-inside: |
        cookie.setSecure(true);
        ...
```
#### Rule2
```yaml
rules:
  - id: secure-flag-not-set
    patterns:
    - pattern: $RESPONSE.addCookie($COOKIE);
    - pattern-not-inside: |
        $COOKIE.setSecure(true);
        ...
```
> [!ERROR] Note that it looks like `VARIABLES` need to be capitalized
> if  "COOKIE" in `$COOKIE.setSecure(true);` was 
#### Example Code that matches this rule:
```csharp
@Controller
public class CookieController {

    @RequestMapping(value = "/cookie1", method = "GET")
    public void setCookie(@RequestParam String value, HttpServletResponse response) {
        Cookie cookie = new Cookie("cookie", value);
        response.addCookie(cookie);
    }

    @RequestMapping(value = "/cookie2", method = "GET")
    public void setSecureCookie(@RequestParam String value, HttpServletResponse response) {
        Cookie cookie = new Cookie("cookie", value);
        cookie.setSecure(true);
        String mystr = "this line should not interfere";
        response.addCookie(cookie); // Try not to match here.
    }
}
```
  
### 5. Metavariable Regex [SemGrep Tutorial](https://semgrep.dev/learn/composition/5)
One final Semgrep pattern type that is very useful is called `metavariable-regex`.
It allows you to specify that certain metavariables **only** match variables whose names fit a specified regular expression.
```yaml
rules:
  - id: use-decimalfield-for-money
    patterns:
    - pattern-inside: |
        class $M(...):
          ...
    - pattern: $F = django.db.models.FloatField(...)
    - metavariable-regex:
        metavariable: '$F'
        regex: '.*(price|fee|salary).*'
    message: Found a FloatField used for variable $F. Use DecimalField for currency fields to avoid float-rounding errors.
    languages: [python]
    severity: ERROR
```
#### Example Code that matches this rule:
```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=64)
    description = models.TextField()
    # this is ok
    price = models.DecimalField(max_digits=6, decimal_places=2)
    # this is also ok
    return_rate = models.FloatField()
    # Semgrep finds this because old_fee ends in the word fee, which is in the regex above
    old_fee = models.FloatField()
    # match this
    price_inc = models.FloatField()
```
  

---    
## Advanced
To catch a case where some how this $VAR  variable is used. We can use `<... $VAR ...>`
**RULE**
```js
resp.write(<... $VAR ...>);
```
This would match something all of these:
```js
resp.write(resp);
resp.write('Response</br>' + resp);
resp.write('Response</br>' + resp + 'foo');
```
  
  
---   
## Running Semgrep
```shell
semgrep --config [path to directory w/ rules or a rule.yml] [directory with files or a file to scan]
```
- No metrics, quiet (`-q`) output with only results, and output in JSON format
```shell
semgrep --config=[path to directory w/ rules or a rule.yml] --metrics=off -q --json --output=myScanOutput.json [directory with files or a file to scan]
```
- `---config auto` uses Semgrep' s built-in rules
```shell
semgrep --config auto [directory to scan]
```
- You can use multiple configs. Here I'm using built-in rules and my other rules in My-Rules directory
```shell
semgrep --config auto --confg ./My-Rules [directory to scan]
```
  

---   
## Presentations
- Semgrep: a lightweight static analysis tool for security consultant and hackers by TrailOfBits: https://youtu.be/O5mh8j7-An8?si=V2-Y9EdlkSgMvOAx
