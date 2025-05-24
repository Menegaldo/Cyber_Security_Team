```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    push rdi
    push rsi
    pop rdi
    pop rsi
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{AxF4rki8-Y3H7V8bbnBzeNwvjhm.dhDMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/8f926f1e-73e9-4424-8c7d-16a9fc0ce900)
