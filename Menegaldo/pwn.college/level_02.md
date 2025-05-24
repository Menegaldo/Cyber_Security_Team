```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    mov rax, 0x1337
    mov r12, 0xCAFED00D1337BEEF
    mov rsp, 0x31337
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{cF6TNuzYaVw0VXJy0RnVCNRZQGQ.QXwEDOzwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/64ff9c19-bcda-407f-b1bc-1042ef11d30b)

