# Linux

## Kali

## Commands

### Sudo Overview

```bash
# Run command as root
sudo cat /etc/shadow
# Switch to root user
sudo su -
sudo -i
```

### Navigating Filesystem

```bash
pwd  # Print working directory
cd  # Change directory
ls  # List files and directories
mkdir  # Make a directory
touch  # Make a file
cp  # Copy a file or directory
rm  # Remove a file or directory
mv  # Move or rename a file or directory
locate  # Locat a file or directory
man  # Manual pages for a command
```

### Users and Privileges

```bash
d rwx r-x r-w  # Directory with permissions 755
- rwx r-- r--  # File with permissions 744
chmod  # Change mode of a file or directory
chown  # Change owernship of a file or directory
adduser  # Adds a new user
passwd  # Change the password for a user
su  # Switch/substitute to a user
```

### Network Commands

```bash
ifconfig / ip a  # Configure a network interface
iwconfig  # Configure a wireless network interface
ping  # Send ICMP ECHO_REQUEST to network hosts
arp  # Resolve IP address to a MAC address
netstat  # Print network connections, tables, and statistics
route / ip r  # Print routing table
```

### Installing and Updating Tools

```bash
apt update  # Updates list of available packages and their versions, but it does not install or upgrade them.
apt upgrade  # Actually installs newer versions of the packages you have.
apt install  # Install software
git clone  # Download software from GitHub or other git provider
```

### Files

```bash
echo "hello" > file.txt  # Send/overwrite text to a file
echo "world" >> file.txt  # Append text to a file
touch  # Create a new file
vim/nano  # CLI file editors
gedit  # UI file editor
cat  # Concatenate files and print on the standard out
```

## Bash Scripting

### Examples

Print out a variable called `str` which contains the string "Hello World!"

```bash
#!/bin/bash
str="Hello World!"
echo $str

./hello.sh
```

Print out the number of arguments (`$#`) and then the argument values.

```bash
#!/bin/bash
echo $#
echo $1 $2

./argtest.sh Hello World
```

Have a user specify an argument number and have it compared against a static argument.

```bash
#!/bin/bash
num1="$1"
num2=17
if [ $num1 -ge $num2 ]
then
  echo "$num1 is greater than or equal to $num2"
else
  echo "$num1 is less than $num2"
fi
echo "Sum is" $(($num1+$num2))

./vartest.sh 19
```

Read input on a newline then reading input again on the same line (`-p`)

```bash
#!/bin/bash
echo "What is your name?"
read name
echo "Hello $name"
echo
read -p "Remind me, what is your name again? " name
echo "Hello again $name"

./readtest.sh
```

For loop iterating through an array.

```bash
#!/bin/bash
names=('Peter' 'Paul' 'Mary' 'David' 'Joe')
x=${#names[@]}
for ((i=0; i<$x; i++))
do
  echo ${names[${i}]}
done

./fortest.sh
```

While loop and then an until loop

```bash
#!/bin/bash
runs=6
while [ $runs -gt 0 ]
do 
  echo "Run down at number $runs"
  let runs=runs-1
done
until [ $runs -gt 6 ]
do
  echo "Run up at number $runs"
  let runs=runs+1
done

./wutest.sh
```

If else loop checking if a directory exists, and if it does, list its contents.

```bash
#!/bin/bash
if [ -d $1 ]
then
  echo "$1 exists"
  ls $1
else
  echo "$1 does not exist."
fi

./iftest.sh
```

Using a function, select statement, and case statement to print out a city chosen.

```bash
#!/bin/bash
function speak
{ if [ $1 = "Paris" ]
  then
    echo "Language used is French"
  elif [ $1 = "Hanoi" ]
  then
    echo "Language used is Vietnamese, with a little French"
  else
    echo "Language used is English, of one form or another!"
  fi
}
PS3=">"
echo "Let's check the language"
select city in "Paris" "Melbourne" "Toronto" "Seattle" "Hanoi" "exit"
do
  if [ $city = "exit" ]
  then
    break
  fi
  case $city in
    Paris)
      echo "City is Paris, France";;
    Melbourne)
      echo "City is Melbourne, Autstralia";;
    Toronto)
      echo "City is Toronto, Canada";;
    Seattle)
      echo "City is Seattle, USA";;
    Hanoi)
      echo "City is Hanoi, Vietnam";;
  esac
  speak $city
done
echo "Bye!"

./fntest.sh
```

## Scripts

### Ping Sweeper

#### Ping

```bash
# Ping example
ping 192.168.4.29
PING 192.168.4.29 (192.168.4.29) 56(84) bytes of data.
64 bytes from 192.168.4.29: icmp_seq1 ttl=128 time=0.403 ms
# Send Ping command/reply to a file
ping 192.168.4.29 > ip.txt
# Grab just the IP address from the file
cat ip.txt | grep "64 bytes" | cut -d " " -f 4 | tr -d ":"
```

#### Script to ping the IP addresses specified for the last octet and grabbing only the IPs

```bash
#!/bin/bash

if [ "$1" == "" ]
then
echo "You forgot an IP address!"
echo "Syntax: ./ipsweep.sh 192.168.4"

else
for ip in `seq 1 254`; do
ping -c 1 $1.$ip | grep "64 bytes" | cut -d " " -f 4 | tr -d ":" &
done
fi
```

#### One Liner executing nmap against IP addresses found

```bash
for ip in $(cat ip.txt); do nmap $ip; done
```
