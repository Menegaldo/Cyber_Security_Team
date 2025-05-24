```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov al,  [0x404000]
    mov bx,  [0x404000]
    mov ecx, [0x404000]
    mov rdx, [0x404000]
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```
pwn.college{Yf-7lUqLoK691FKGwMNsHuQs0W0.dRDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/36a73fb0-6922-4bea-8fb3-c1b44c8e009a)
