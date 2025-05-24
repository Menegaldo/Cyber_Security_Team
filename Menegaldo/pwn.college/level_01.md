```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rdi, 0x1337
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{QN8sx_elrGAjOt4UKyGk1jphso1.dRTOxwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/f542a952-5563-456c-a9ad-319612edc26e)

