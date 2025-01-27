wpscan --url IP --api-token api-token --disable-tls-checks --plugins-detection aggressive --detection-mode aggressive -e ap -o wpscan-out

wpscan --url IP  --disable-tls-checks --plugins-detection aggressive --detection-mode aggressive -e ap -o wpscan-out

wpscan --url IP -e u --disable-tls-checks

wpscan --url IP -U  --disable-tls-checks


wpscan --url IP -U wps-users.txt -P /usr/share/wordlists/metasploit/unix_passwords.txt --disable-tls-checks