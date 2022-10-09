# Interesting Files

## File Types

### zip

```bash
# Try to unzip file
unzip filename.zip
# If a passwowrd is needed, attempt to crack it
zip2john filename.zip >> filename.zip.hash
john filename.zip.hash --wordlist=/usr/share/wordlist/rockyou.txt
```

### rar

```bash
unrar e filename.rar
rar2john filename.rar >> filename.rar.hash
john filename.rar.hash --wordlist=/usr/share/wordlist/rockyou.txt
```

### pfx

```bash
# Try to view file info
openssl pkcs12 -in filename.pfx -info
# If a password is needed, attempt to crack it
pfx2john filename.pfx >> filename.pfx.hash
john filename.pfx.hash --wordlist=/usr/share/wordlist/rockyou.txt
```

After putting in the password it will extract certificates and private keys.

```bash
# Extract the keys
openssl pkcs12 -in filename.pfx -nocerts -out key.pem -nodes
# Extract the certificates
openssl pkcs12 -in filename.pfx -nokeys -out key.cert
```

If the WinRM port (5985,5986) is open it could be possible to login to the target using Evil-WinRm.

```bash
evil-winrm -S -i <IP> -c key.cert -k key.pem
```



