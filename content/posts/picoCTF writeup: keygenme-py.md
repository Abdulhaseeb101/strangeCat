---
title: "picoCTF writeups: keygenme-py"
dated: 2022-04-02
summary: " "
---
This challenge provides no description or hints whatsoever. We are only provided a python file called **keygenme-trial.py**. 

Upon inspecting the file we are presented with a bunch of global variables that seem critical to the program's functioning.

```python
username_trial = "SCHOFIELD"
bUsername_trial = b"SCHOFIELD"

key_part_static1_trial = "picoCTF{1n_7h3_|<3y_of_"
key_part_dynamic1_trial = "xxxxxxxx"
key_part_static2_trial = "}"
key_full_template_trial = key_part_static1_trial + key_part_dynamic1_trial + key_part_static2_trial
```

The flag or in this case the key is revealed except the dynamic part. The key is made of **key_part_static1_trial** + **key_part_dynamic1_trial** + **key_part_static2_trial**. Since we already have **key_part_static1_trial** and **key_part_static2_trial** the challenge is to find the dynamic part of the key.

Diving deeper into the code we find **enter_license()** which calls **check_key()** and this is where the magic happens:

```python
def check_key(key, username_trial):

    global key_full_template_trial

    if len(key) != len(key_full_template_trial):
        return False
    else:
        # Check static base key part --v
        i = 0
        for c in key_part_static1_trial:
            if key[i] != c:
                return False

            i += 1
```
This part checks whether the user entered key matches the 1st static part then further down it proceeds in checking the dynamic part:

```python
if key[i] != hashlib.sha256(username_trial).hexdigest()[4]:
    return False
else:
    i += 1

if key[i] != hashlib.sha256(username_trial).hexdigest()[5]:
    return False
else:
    i += 1

if key[i] != hashlib.sha256(username_trial).hexdigest()[3]:
    return False
else:
    i += 1

if key[i] != hashlib.sha256(username_trial).hexdigest()[6]:
    return False
else:
    i += 1

if key[i] != hashlib.sha256(username_trial).hexdigest()[2]:
    return False
else:
    i += 1

if key[i] != hashlib.sha256(username_trial).hexdigest()[7]:
    return False
else:
    i += 1

if key[i] != hashlib.sha256(username_trial).hexdigest()[1]:
    return False
else:
    i += 1

if key[i] != hashlib.sha256(username_trial).hexdigest()[8]:
    return False
```
We can see that the code takes the **username_trial** global, encrypts with sha256 and picks several specific indices from the hex digest it produces i.e **4,5,3,6,2,7,1,8**. We can generate the key by writing a script to do just that:
```python
import hashlib
import base64


keyPartStatic1Trial = "picoCTF{1n_7h3_|<3y_of_"
keyPartStatic2Trial = "}"

usernameTrial = b"SCHOFIELD"

dynKey = ""

# positions in usernameTrial
positions = [4,5,3,6,2,7,1,8]

for p in positions:
    dynKey += hashlib.sha256(usernameTrial).hexdigest()[p]

key = keyPartStatic1Trial + dynKey + keyPartStatic2Trial
print(key)
```
### Output
```
picoCTF{1n_7h3_|<3y_of_e584b363}
```
Great!. Happy Pwning !!.
