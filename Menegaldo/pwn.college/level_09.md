```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, rdi
    shr rax, 32
    shl rax, 56
    shr rax, 56
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{Mc3vwAK-yTjoXo2fk0N6pWH-vrw.dBDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/45afe68d-6869-423d-87bc-5121b119fe04)
