
### Dirsearch 
#dirsearch

```
dirsearch -u http://IP -e html,txt,php,asp -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -r -R 2
```
### Gobuster
#### vhost
```
gobuster vhost --append-domain -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u Marshalled.pg 
```
#### DNS enumeration


### ffuf
#ffuf
http://ffuf.me/cd/basic
```
ffuf -recursion -mc all -ac -c -e .htm,.shtml,.php,.html,.js,.txt,.zip,.bak,.asp,.aspx,.xml -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://192.168.233.151/FUZZ
```
Its good to add these to the match list when working with web apis
`-mc 405 415` along with `200,204,301,302,307,401,403`
We can use `-ac` to auto calibrate and filter out garbage

### wfuzz
#wfuzz
```
wfuzz -u http://IP/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 400,403,404 --hw 0 -hh 478
```