---
title: "PicoCTF 2022 Writeup"
date: 2022-04-04
excerpt_separator: "<!--more-->"
categories:
  - Write Up
tags:
  - CTF
  - Write Up
  - Cyber Security
---

This year, I participated in the PicoCTF 2022 competition. It was a relatively easy competition. The guides below were done on the first night of the challenge. I would have continued participation, but I was inundated with commitments. Most of the challenges I attempted were under the *Reverse Engineering* category and few were under *Cryptography*. After April, I will have time to participate properly in CTFs again.

# Reverse Engineering
## file-run 1
**Description**:
A program has been provided to you, what happens if you try to run it on the command line?

This was a nice and easy `strings` solve.

## file-run 2
**Description**:
Another program, but this time, it seems to want some input. What happens if you try to run it on the command line with input "Hello!"?

This was another easy `strings` solve.

## patchme.py
**Description**:
Can you get the flag?

This was a pretty simple solve. By commenting out the `if` statement presented and moving `decryption` inline with the rest of the function code, `level_1_pw_check()` will always output the decoded flag.

```python
def level_1_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    #if( user_pw == "ak98" + \
    #               "-=90" + \
    #               "adfjhgj321" + \
    #               "sleuth9000"):
    #    print("Welcome back... your flag, user:")
    decryption = str_xor(flag_enc.decode(), "utilitarian")
    print(decryption)
    return
    print("That password is incorrect")
```
___

## Safe Opener
**Description**:
Can you open this safe? I forgot the key to my safe but this program is supposed to help me with retrieving the lost key. Can you help me unlock my safe?

After downloading the file and inspecting the code, it is clear that `encodedkey` in the `openSafe` function is encoded in base64. By taking that string to the world famous [Cyber Chef](https://gchq.github.io/CyberChef/), the flag is retrieved. 

```java
    public static boolean openSafe(String password) {
        String encodedkey = "cGwzYXMzX2wzdF9tM18xbnQwX3RoM19zYWYz";
        
        if (password.equals(encodedkey)) {
            System.out.println("Sesame open");
            return true;
        }
        else {
            System.out.println("Password is incorrect\n");
            return false;
        }
    }
```
___

## unpackme.py
**Description**:
This was an easy *add a line of  code* problem. The the `.py` file provided,  this print statement on the second last-line of the script.
```Python
plain = f.decrypt(payload)
print(plain) // added line
exec(plain.decode())
```

___

## bloat.py
**Description**:
Can you get the flag?

This is a nice obfuscation problem that looks confusing at first, but quickly the logic of the functions can be exploited. By looking at the program, it is clear that the function `arg133(arg432)` is the only function which rejects user input and closes the program. So, rather than try to deobfuscate the problem, the simplest solution is to again change what happens at the `if` statement to prevent the closing of the program.

```python
\\ Orginal Function
def arg133(arg432):
  if arg432 == a[71]+a[64]+a[79]+a[79]+a[88]+a[66]+a[71]+a[64]+a[77]+a[66]+a[68]:
    return True
  else:
    print(a[51]+a[71]+a[64]+a[83]+a[94]+a[79]+a[64]+a[82]+a[82]+a[86]+a[78]+\
a[81]+a[67]+a[94]+a[72]+a[82]+a[94]+a[72]+a[77]+a[66]+a[78]+a[81]+\
a[81]+a[68]+a[66]+a[83])
    sys.exit(0)
    return False

\\ Modified Function
def arg133(arg432):
  if arg432 == a[71]+a[64]+a[79]+a[79]+a[88]+a[66]+a[71]+a[64]+a[77]+a[66]+a[68]:
    return True
  else:
    return True
```

___
___
# Cryptography
## basic-mod1
**Description**:
We found this weird message being passed around on the servers, we think we have a working decryption scheme. Take each number mod 37 and map it to the following character set: 0-25 is the alphabet (uppercase), 26-35 are the decimal digits, and 36 is an underscore.

This is the Python Script I wrote that maps and prints the encrypted flag.

```python
code = [128, 63, 242, 87, 151, 147, 50, 369, 239, 248, 205, 346, 299, 73, 335, 189, 105, 293, 37, 214, 333, 137]
decoder = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, '_']

mod_decode = []

for number in code:
	mod_decode.append(number % 37)

decoded = []

for position in mod_decode:
	decoded.append(decoder[position])

listToStr = ''.join([str(elem) for elem in decoded])
	
print(listToStr)
```

___
## credstuff
**Description**:
We found a leak of a blackmarket website's login credentials. Can you find the password of the user `cultiris` and successfully decrypt it? The first user in `usernames.txt` corresponds to the first password in `passwords.txt`. The second user corresponds to the second password, and so on.

Here, you are given two `.txt` files. By using `crtl-f` (or OS equivalent) to search for `cultiris` in  `username.txt` with your text-editor of choice, you quickly find the row this user is on. Following that, go to that position in the `passwords.txt` file. That password is the flag.

___

## morse-code
**Description**:
Morse code is well known. Can you decrypt this?

This is a fairly simple challenge. You are given a `.wav` file which contains morse-code. By uploading this file to [Morse Decoder](https://morsecode.world/international/decoder/audio-decoder-adaptive.html), the flag will be automatically generated for you.

___

## substitution0
**Description**:
A message has come in but it seems to be all scrambled. Luckily it seems to have the key at the beginning. Can you crack this substitution cipher?

This challenge was easily solved using the [Substitution Cipher Decoder](https://planetcalc.com/8047/)

___

## substitution2
**Description**:
It seems that another encrypted message has been intercepted. The encryptor seems to have learned their lesson though and now there isn't any punctuation! Can you still crack the cipher?

Using the  [Substitution Cipher Decoder](https://planetcalc.com/8047/) again, I was able to obtain the flag. 

As this tool was capable of solving the two challenges shown, I am unsure if *substitution1* was having issues with the flag. I believe I obtained it with the same method. It had an incredibly low rating the night I participated. 



