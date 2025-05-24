```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    pop rax
    sub rax, rdi
    push rax
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{gXY1BtY7yxywJTkuKz70FKOWZSl.ddDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/728f66ab-89fb-4fad-979d-93c8a6d359f4)
