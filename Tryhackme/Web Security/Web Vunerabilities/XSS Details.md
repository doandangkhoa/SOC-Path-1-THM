---
title: XSS Details
updated: 2026-04-03 16:12:31Z
created: 2026-04-02 15:04:32Z
latitude: 22.27602200
longitude: 114.17514710
altitude: 0.0000
---

# Reflected XSS

### 1\. PHP

**Vulnerable code**

```php
<?php
$search_query = $_GET['q'];
echo "<p>You searched for: $search_query</p>";
?>
```

**Fixed code**

```php
<?php
$search_query = $_GET['q'];
$escaped_search_query = htmlspecialchars($search_query);
echo "<p>You searched for: $escaped_search_query</p>";
?>
```

The PHP function `htmlspecialchars()` converts special characters to HTML entities. The characters `<`, `>`, `&`, `"`, `'` are replaced by default to prevent scripts in the input from executing.

### 2\. JavaScript (Node.js)

**Vulnerable code**

```javascript
const express = require('express');
const app = express();

app.get('/search', function(req, res) {
    var searchTerm = req.query.q;
    res.send('You searched for: ' + searchTerm);
});

app.listen(80);
```

**Fixed code**

```javascript
const express = require('express');
const sanitizeHtml = require('sanitize-html');

const app = express();

app.get('/search', function(req, res) {
    const searchTerm = req.query.q;
    const sanitizedSearchTerm = sanitizeHtml(searchTerm);
    res.send('You searched for: ' + sanitizedSearchTerm);
});

app.listen(80);
```

The solution is achieved by using the `sanitizeHtml()` from the `sanitize-html` library. This function removes unsafe elements and attributes. This includes removing script tags, among other elements that could be used for malicious purposes.

### 3\. Python (Flask)

**Vulnerable code**

```python
from flask import Flask, request

app = Flask(__name__)

@app.route("/search")
def home():
    query = request.args.get("q")
    return f"You searched for: {query}!"

if __name__ == "__main__":
    app.run(debug=True)
```

**Fixed code**

```python
from flask import Flask, request
from html import escape

app = Flask(__name__)

@app.route("/search")
def home():
    query = request.args.get("q")
    escaped_query = escape(query)
    return f"You searched for: {escaped_query}!"

if __name__ == "__main__":
    app.run(debug=True)
```

The main change is that the user input is now escaped using the `escape()` function from the `html` module. Note that the `html.escape()` function in Flask is actually an alias for `markupsafe.escape()`. They both come from the Werkzeug library and serve the same purpose: escaping unsafe characters in strings. This function converts characters like `<`, `>`, `"`, `'` to HTML escaped entities, disarming any malicious code the user has inserted.

### 4\. ASP.NET

**Vulnerable code**

```C#
public void Page_Load(object sender, EventArgs e)
{
    var userInput = Request.QueryString["q"];
    Response.Write("User Input: " + userInput);
}
```

**Fixed code**

```C#
using System.Web;

public void Page_Load(object sender, EventArgs e)
{
    var userInput = Request.QueryString["q"];
    var encodedInput = HttpUtility.HtmlEncode(userInput);
    Response.Write("User Input: " + encodedInput);
}
```

`HttpUtility.HtmlEncode()` method, which converts various characters, such as `<`, `>`, and `&`, into their respective HTML entity encoding.

# Stored XSS

## Vulnerable Web Application

There are many reasons for a web application to be vulnerable to stored XSS. Some of the best practices to prevent stored XSS vulnerabilities are:

- **Validate and sanitize input**: Define clear rules and enforce strict validation on all user-supplied data. For instance, only alphanumeric characters can be used in a username, and only integers can be allowed in age fields.
- **Use output escaping**: When displaying user-supplied input within an HTML context, encode all HTML-specific characters, such as `<`, `>`, and `&`.
- **Apply context-specific encoding**: For instance, within a JavaScript context, we must use JavaScript encoding whenever we insert data within a JavaScript code. On the other hand, data placed in URLs must use relevant URL-encoding techniques, like percent-encoding. The purpose is to ensure that URLs remain valid while preventing script injection.
- **Practice defence in depth**: Don’t rely on a single layer of defence; use server-side validation instead of solely relying on client-side validation.

### 1\. PHP

**Vulnerable Code**

```php
// Storing user comment
$comment = $_POST['comment'];
mysqli_query($conn, "INSERT INTO comments (comment) VALUES ('$comment')");

// Displaying user comment
$result = mysqli_query($conn, "SELECT comment FROM comments");
while ($row = mysqli_fetch_assoc($result)) {
    echo $row['comment'];
}
```

**Fixed Code**

```php
// Storing user comment
$comment = mysqli_real_escape_string($conn, $_POST['comment']);
mysqli_query($conn, "INSERT INTO comments (comment) VALUES ('$comment')");

// Displaying user comment
$result = mysqli_query($conn, "SELECT comment FROM comments");
while ($row = mysqli_fetch_assoc($result)) {
    $sanitizedComment = htmlspecialchars($row['comment']);
    echo $sanitizedComment;
}
```

Before displaying every comment on the screen, we pass it through the `htmlspecialchars()` function to ensure all special characters are converted to HTML entities. Consequently, any attempts for stored XSS won’t make it to the end user’s browser.

This is outside the scope of this room; however, if you are curious about the SQL injection vulnerability, this is alleviated using the `mysqli_real_escape_string()`. This function escapes special characters in the input string so it can safely be used in an SQL statement.

### 2\. JavaScript (Node.js)

**Vulnerable Code**

```javascript
app.get('/comments', (req, res) => {
  let html = '<ul>';
  for (const comment of comments) {
    html += `<li>${comment}</li>`;
  }
  html += '</ul>';
  res.send(html);
});
```

**Fixed Code**

```javascript
const sanitizeHtml = require('sanitize-html');

app.get('/comments', (req, res) => {
  let html = '<ul>';
  for (const comment of comments) {
    const sanitizedComment = sanitizeHtml(comment);
    html += `<li>${sanitizedComment}</li>`;
  }
  html += '</ul>';
  res.send(html);
});
```

Part of the solution is sanitizing the HTML before displaying it to the user. We can remove HTML elements outside the allowlist using the `sanitizeHTML()` function. In general, we expect to allow basic text formatting such as bold and italic (`<b>` and `<i>`), but we would remove potentially dangerous or unsafe elements such as `<script>` and `<onload>`.

### 3\. Python (Flask)

**Vulnerable Code**

```python
from flask import Flask, request, render_template_string
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'
db = SQLAlchemy(app)

class Comment(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.String, nullable=False)

@app.route('/comment', methods=['POST'])
def add_comment():
    comment_content = request.form['comment']
    comment = Comment(content=comment_content)
    db.session.add(comment)
    db.session.commit()
    return 'Comment added!'

@app.route('/comments')
def show_comments():
    comments = Comment.query.all()
    return render_template_string(''.join(['<div>' + c.content + '</div>' for c in comments]))
```

**Fixed Code**

```python
from flask import Flask, request, render_template_string, escape
from flask_sqlalchemy import SQLAlchemy
from markupsafe import escape

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'
db = SQLAlchemy(app)

class Comment(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.String, nullable=False)

@app.route('/comment', methods=['POST'])
def add_comment():
    comment_content = request.form['comment']
    comment = Comment(content=comment_content)
    db.session.add(comment)
    db.session.commit()
    return 'Comment added!'

@app.route('/comments')
def show_comments():
    comments = Comment.query.all()
    sanitized_comments = [escape(c.content) for c in comments]
    return render_template_string(''.join(['<div>' + comment + '</div>' for comment in sanitized_comments]))
```

We used the `escape()` function to ensure that any special characters in the user-submitted comment are replaced with HTML entities. As you would expect, the characters `&`, `<`, `>`, `'`, and `"` are converted to HTML entities (`&amp;`, `&lt;`, `&gt;`, `&#39;`, and `&quot;`). We made two changes:

Although the user-submitted input `request.form['comment']` is saved verbatim, the content of each saved comment `c` goes through the escape() function before it is sent to the user’s browser to be displayed as HTML.

### 4\. C# (ASP.NET)

**Vulnerable Code**

```csharp
public void SaveComment(string userComment)
{
    var command = new SqlCommand("INSERT INTO Comments (Comment) VALUES ('" + userComment + "')", connection);
    // Execute the command
}

public void DisplayComments()
{
    var reader = new SqlCommand("SELECT Comment FROM Comments", connection).ExecuteReader();
    while (reader.Read())
    {
        Response.Write(reader["Comment"].ToString());
    }
    // Execute the command
}
```

**Fixed Code**

```csharp
using System.Web;

public void SaveComment(string userComment)
{
    var command = new SqlCommand("INSERT INTO Comments (Comment) VALUES (@comment)", connection);
    command.Parameters.AddWithValue("@comment", userComment);
}

public void DisplayComments()
{
    var reader = new SqlCommand("SELECT Comment FROM Comments", connection).ExecuteReader();
    while (reader.Read())
    {
        var comment = reader["Comment"].ToString();
        var sanitizedComment = HttpUtility.HtmlEncode(comment);
        Response.Write(sanitizedComment);
    }
    reader.Close();
}
```

With a few changes, the code’s security has improved. Stored-XSS is fixed by using the `HttpUtility.HtmlEncode()` method before displaying the `userComment` as part of a web page. (If you are curious, the SQL injection vulnerability is fixed by using parametrized SQL queries with values passed separately instead of building the SQL query via string concatenation. This can be achieved using the `Parameters.AddWithValue()` method in the `SqlCommand` objects.

# Context and Evasion

## Context

The injected payload will most likely find its way within one of the following:

- Between HTML tags
- Within HTML tags
- Inside JavaScript

When XSS happens between HTML tags, the attacker can run `<script>alert(document.cookie)</script>`.

However, when the injection is within an HTML tag, we need to end the HTML tag to give the script a turn to load. Consequently, we might adapt our payload to `><script>alert(document.cookie)</script>` or `"><script>alert(document.cookie)</script>` or something similar that would fit in the context.

We might need to terminate the script to run the injected one if we can inject our XSS within an existing JavaScript. For instance, we can start with `</script>` to end the script and continue from there. If your code is within a JavaScript string, you can close the string with `'`, complete the command with a semicolon, execute your command, and comment out the rest of the line with `//`. You can try something like this `';alert(document.cookie)//`.

## Evasion

&nbsp;