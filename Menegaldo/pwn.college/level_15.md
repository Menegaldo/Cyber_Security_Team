```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("mov al, [0x404000]")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{sX5nhGoThCLgFC5aubsn3aHe_Me.QX0EDOzwyMyITMyEzW}

![image](https://github.com/user-attachments/assets/c5bea278-7acc-4cf2-8f53-0b5f8a1d92d9)

