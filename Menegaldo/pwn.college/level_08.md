```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov al, dil
    mov bx, si
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{IpuBH7nVHl-i-FALsjxLvdLENGL.dlTOxwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/57511169-157c-491f-b511-a20238f9f15d)


