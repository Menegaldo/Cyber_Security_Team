```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, [0x404000]
    add qword ptr [0x404000], 0x1337
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{obJW-5LnMYvjFfmvVl84hd58lWY.dNDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/77d50738-c20c-4d2f-80a9-fb2a39e36592)
