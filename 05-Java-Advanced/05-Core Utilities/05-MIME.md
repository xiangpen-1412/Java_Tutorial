# MIME and MimeMessage

## 1. What is MIME?

MIME stands for:

```text
Multipurpose Internet Mail Extensions
```

You can understand MIME as a set of rules that tells internet systems:

```text
what type of content this is
how this content should be encoded
how multiple parts should be organized
```

MIME was originally created for email, but later it became widely used in HTTP as well.

---

## 2. Why was MIME created?

Early email systems were mainly designed to send plain text.

But real emails need more than plain text:

- HTML content
- images
- PDF files
- Word documents
- attachments
- Chinese or other non-ASCII characters

So MIME was created to extend email from simple plain text to more complex content.

---

## 3. MIME type / Content-Type

The most common thing you see from MIME is `Content-Type`.

Examples:

```http
Content-Type: text/plain
```

```http
Content-Type: text/html
```

```http
Content-Type: application/json
```

```http
Content-Type: image/png
```

```http
Content-Type: application/pdf
```

These are all MIME types.

They tell the receiver what kind of data is being sent.

---

## 4. MIME is also used in HTTP

Although MIME started from email, HTTP also uses MIME types heavily.

For example, when a backend returns JSON:

```http
Content-Type: application/json
```

When a browser loads an image:

```http
Content-Type: image/png
```

When a form uploads files:

```http
Content-Type: multipart/form-data
```

So MIME is not only about email. It is a general internet content-type system.

---

## 5. MIME and Base64

MIME often works together with Base64.

Why?

Because email protocols historically were not good at handling raw binary files.

Attachments like PDF, images, and Word documents are binary data.

So MIME can mark a part as Base64 encoded:

```text
Content-Transfer-Encoding: base64
```

That means:

```text
this file has been converted from binary into text using Base64
```

Example inside an email:

```text
Content-Type: application/pdf
Content-Transfer-Encoding: base64

JVBERi0xLjQK...
```

Here:

- `Content-Type` says this part is a PDF
- `Content-Transfer-Encoding` says the PDF content is Base64 encoded

---

## 6. multipart

Modern emails often contain multiple parts.

For example:

```text
email body
+ HTML body
+ image
+ PDF attachment
```

MIME uses `multipart` to describe this structure.

Common multipart types:

```text
multipart/mixed
multipart/alternative
multipart/related
multipart/form-data
```

---

## 6.1 multipart/mixed

Used when an email contains normal content plus attachments.

Example:

```text
Part 1: email body
Part 2: PDF attachment
Part 3: image attachment
```

---

## 6.2 multipart/alternative

Used when the same content has multiple versions.

Example:

```text
Part 1: plain text version
Part 2: HTML version
```

The email client can choose the best version to display.

---

## 6.3 multipart/related

Used when HTML content depends on related resources, such as inline images.

Example:

```text
Part 1: HTML body
Part 2: inline image used by the HTML
```

---

## 6.4 multipart/form-data

Used in HTTP file upload.

Example:

```http
Content-Type: multipart/form-data
```

This is commonly used when the frontend uploads files to the backend.

---

## 7. What is MimeMessage?

In Java, `MimeMessage` is an object that represents a complete MIME email.

Depending on the Java version and dependency, it may come from:

```java
jakarta.mail.internet.MimeMessage
```

or older projects may use:

```java
javax.mail.internet.MimeMessage
```

You can understand it as:

```text
a Java object for building and representing a full email
```

It can contain:

- sender
- receiver
- subject
- plain text body
- HTML body
- attachments
- inline images
- CC
- BCC
- MIME headers

---

## 8. Why do we need MimeMessage?

A real email is not just a simple string.

A modern email may contain:

```text
headers
plain text body
HTML body
attachments
inline images
encoding information
multiple MIME parts
```

So Java uses `MimeMessage` to represent the full email structure.

---

## 9. JavaMailSender and MimeMessage

In Spring Boot, email sending often uses:

```java
JavaMailSender
```

A common pattern is:

```java
MimeMessage message = mailSender.createMimeMessage();
```

This creates a new MIME email object.

Then you set the email content and send it.

---

## 10. MimeMessageHelper

`MimeMessage` itself is relatively low-level.

Spring provides `MimeMessageHelper` to make it easier to build emails.

Example:

```java
MimeMessage message = mailSender.createMimeMessage();

MimeMessageHelper helper = new MimeMessageHelper(message, true, "UTF-8");

helper.setTo("user@example.com");
helper.setSubject("Reset your password");
helper.setText("<h1>Click the link to reset your password</h1>", true);

mailSender.send(message);
```

Important part:

```java
new MimeMessageHelper(message, true, "UTF-8")
```

The second parameter `true` means this message supports multipart content.

That is needed if you want to add attachments or inline images.

---

## 11. Plain text email

```java
MimeMessage message = mailSender.createMimeMessage();
MimeMessageHelper helper = new MimeMessageHelper(message, false, "UTF-8");

helper.setTo("user@example.com");
helper.setSubject("Hello");
helper.setText("This is a plain text email.");

mailSender.send(message);
```

Here the email body is plain text.

---

## 12. HTML email

```java
MimeMessage message = mailSender.createMimeMessage();
MimeMessageHelper helper = new MimeMessageHelper(message, true, "UTF-8");

helper.setTo("user@example.com");
helper.setSubject("Welcome");
helper.setText("<h1>Welcome</h1><p>Thanks for registering.</p>", true);

mailSender.send(message);
```

The second argument of `setText` is:

```java
true
```

That means the content should be treated as HTML.

---

## 13. Email with attachment

```java
MimeMessage message = mailSender.createMimeMessage();
MimeMessageHelper helper = new MimeMessageHelper(message, true, "UTF-8");

helper.setTo("user@example.com");
helper.setSubject("Your document");
helper.setText("Please check the attachment.");

helper.addAttachment("document.pdf", new File("document.pdf"));

mailSender.send(message);
```

Behind the scenes, this becomes a MIME multipart email.

The attachment content may be encoded using Base64.

---

## 14. What does a real MIME email look like?

A simplified raw email may look like this:

```text
From: sender@example.com
To: user@example.com
Subject: Hello
Content-Type: multipart/mixed; boundary="abc123"

--abc123
Content-Type: text/html; charset=UTF-8

<h1>Hello</h1>

--abc123
Content-Type: application/pdf
Content-Transfer-Encoding: base64
Content-Disposition: attachment; filename="document.pdf"

JVBERi0xLjQK...

--abc123--
```

This shows the relationship clearly:

- MIME defines the structure
- Content-Type describes each part
- Base64 encodes binary attachments
- MimeMessage represents this whole email in Java

---

## 15. Relationship between Base64, MIME, and MimeMessage

### Base64

```text
binary data -> text
```

It is an encoding method.

---

### MIME

```text
content type + message structure + transfer encoding
```

It is a content format standard.

---

### MimeMessage

```text
Java object for a complete MIME email
```

It is Java's object representation of a MIME email.

---

## 16. Summary

You can remember them like this:

```text
Base64 = how to convert binary data into text
MIME = how to describe and organize internet/email content
MimeMessage = Java object used to build a MIME email
```

In a Spring Boot password reset feature, the flow is usually:

```text
1. Generate reset token
2. Put token into reset link
3. Build HTML email with MimeMessage
4. Send the email
```

So Base64 and MimeMessage often appear together in real backend development.
