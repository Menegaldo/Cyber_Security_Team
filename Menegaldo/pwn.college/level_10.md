```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    lea rax, [rdi]
    and rax, rsi
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{0R0A6-fJw0mVwVHRko_WLfVgSRo.dFDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/bdbccd65-6d27-4e1b-882b-db43bec9883f)
