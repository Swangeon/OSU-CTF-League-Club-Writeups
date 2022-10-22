## Info On Challenge
Challenge Description: One time pads are a way of encrypting a message in a way that cannot be cracked. I have developed the Super One Time Pad to increase its security! Good Luck trying to find the flag!

## Code
(Yours may not come with comments)
```Python
#!/usr/bin/python3
from os import urandom
from base64 import b64encode
from flag import FLAG
MAX_LENGTH = 60

# performs an XOR in the two byte objects and returns the result
def xor_bytes(b1:bytes, b2:bytes) -> bytes:
    return bytes(x ^ y for x, y in zip(b1, b2))

# xor all byte objects in a list with eachother
def xor_list(bytes_list) -> bytes:
    result = bytes_list[0]

    for b in bytes_list[1:]:
        result = xor_bytes(result, b)
    
    return result


if __name__ == '__main__':
    # get 3 one time pads
    otps = [urandom(MAX_LENGTH) for _ in range(3)]

    # combine the one time pads into a "super" one time pad
    super_otp = xor_list(otps)

    # encrypt the flag using the super one time pad
    enc_flag = xor_bytes(FLAG.encode(), super_otp)
    
    # print out the encrypted flag
    print(f'The encrypted flag in base 64:')
    print(b64encode(enc_flag).decode())

    for otp in otps:
        # get the message that the user wants to encrypt (up to 60 characters)
        print('Enter message you would like to encrypt (input truncated to 60 characters): ')
        user_message = str(input())[:60]

        # encrypt the message using xor
        enc_message = xor_bytes(user_message.encode(), otp)

        # print encrypted message
        print(f'Your encrypted message is in base 64:')
        print(b64encode(enc_message).decode())
```

## Function Imports 
- `urandom`
	- **Argument(s) - Size:** It is the size of string random bytes
	- **Return Value:** This function returns a string which represents random bytes suitable for cryptographic use.
- `b64encode`
	- **Argument(s) - Bytes String:** Enter a bytes type string to encode
	- **Return Value:** This function returns a string which is base64 encoded.
- `FLAG` 
	- Just holds the string to the flag on the server side

## In Program Defined Functions
- `xor_bytes`
	- **Argument(s) - b1, b2:** Bytes String/Character
	- **Return Value:** This function returns b1 XOR'ed with b2  
- `xor_list`
	- **Argument(s) - Array:** An array that is supposed to have multiple bytes like strings in it
	- **Return Value:** This function returns result which holds the first element in the array XOR'ed with the others as it uses `xor_bytes`

## Solution
1. After the program sets everything up we can see that it makes an array variable `otps`  that has 3 random byte strings using `urandom` of length `MAX_LENGTH` which equals 60
2. To make the Super One Time Pass the challenge talks about it will make the the `super_otp` variable that uses the  `xor_list` function to create our Super One Time Pass
3. Next, the program encrypts the flag using the super otp and stores it in the variable `enc_flag` which gets outputed to the user in base64
	- We want to save this output for later
4. A for loop starts and this is where our exploitation of XOR encryption starts as the program encrypts our input with the key
	- A trick with XOR is that anything XOR'ed with 0 is itself
	- Using this trick we can supply the program '\\x00' (being actual zeros not ascii zeros) through pwntools (because the input function in python always converts to string/ascii representations)
	- Because it is using each key for the three inputs the program wants we can find out each key the flag was encrypted with so long as we base64 decode everything

## Solution Python Code
```Python
from base64 import *
from pwn import *

p = process("./super_otp.py")
# p = remote("server address", port number)

p.recvline()
base64_encoded_flag = p.recvline()
p.recvline()

encrypted_flag = base64.b64decode(base64_encoded_flag)

print(f"Encrypted_Flag = {encrypted_flag}")

# We send zeros of MAX_LENGTH because anything XOR'ed by 0 is itself and on the server side it is taking the key and XOR'ing it with our input
p.sendline(b'\x00' * 60)
p.recvline()
k1 = p.recvline()
p.recvline()
p.sendline(b'\x00' * 60)
p.recvline()
k2 = p.recvline()
p.recvline()
p.sendline(b'\x00' * 60)
p.recvline()
k3 = p.recvline()

# Print out keys
k1 = base64.b64decode(k1)
print(f"Key 1 = {k1}")
k2 = base64.b64decode(k2)
print(f"Key 2 = {k2}")
k3 = base64.b64decode(k3)
print(f"Key 3 = {k3}")

result = ""

# We want to XOR each byte in the flag with each corresponding byte in the keys that we found 
for idx, byte in enumerate(encrypted_flag):
	# Remeber, it doesn't matter in what order you XOR the byte and want to use chr to get he character
	result += chr(b ^ k1[idx] ^ k2[idx] ^ k3[idx])

print(f"Flag = {result}")
```

## Flag
The flag is **osu{nev3r_R3uSE_On3_7iM3_P@D$}**
