```py
from pwn import asm, context, process

context.update(arch='amd64', os='linux')

shellcode = asm("""
    cmp rdi, 3
    ja default_case
    jmp [rsi + rdi*8]
default_case:
    jmp [rsi + 32]
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{A7byt4749GSMJw8P-PnFSxZ-VOg.dJTMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/d10d1bc0-06cb-423d-bc57-86a3beacc91d)
