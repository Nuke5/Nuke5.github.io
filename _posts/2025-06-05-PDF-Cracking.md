---
title: "PDF Decryption"
categories:
  - Blog
tags:
  - Decryption
  - Linux
  - Mpesa
---
Mpesa statements are usually password protected using either your ID number as the password, a combination of your ID plus a random 4 digit character and as from atleast 2022 to 2023 they text you a 6 digit password. I have been unable to get these messages nowadays and lost the archived messages for some of the older statements they sent. However since the format has not changed so far we can build a wordlist thats just a little over 1 million entries long if you know the ID number. This keeps the wordlist to a manageble size of about 7MB.
used with pdfcrack
Requirements

- A linux environment (Bare metal, Virtual Machine or WSL works) to run PDFcrack.

- Python installed.

### Creating the wordlist

```python
# This program generates a wordlist file for pdfcrack to use 
# Ask the user for ID number and assign it to varialble national_ID
national_ID = input("Enter National ID ")
#create brute.txt file and  write to file 
filename ="wordlist.txt"

with open(filename,"w") as file:
    file.write(national_ID+"\n")
# generate 6 numerical passwords
def generate_6_digits():
    for x in range(1000000):
        yield f'{x:06}'
def write_to_file(filename):
    with open(filename,"a") as f:
        for number in generate_6_digits():
            f.write(number+"\n")
write_to_file("wordlist.txt")
# generate 4 numerical passwords and concatinate with national_ID
def generate_4_digits():
    for x in range(10000):
        yield f'{x:04}'
def write_to_file(filename):
    with open(filename,"a") as f:
        for number in generate_4_digits():
            # contatinate ith national_ID and hyphen
            f.write(national_ID+"-"+number+"\n")
write_to_file("wordlist.txt")


```
