```py
#!/usr/bin/env python3

from pwn import asm, context, process

context.update(arch='amd64')

shellcode = asm("""
    test rdi, rdi
    je done

    xor rax, rax
loop:
    mov bl, byte ptr [rdi + rax]
    test bl, bl
    je done
    inc rax
    jmp loop

done:
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{8XYnZZtIJDFTGTR_NzBILcCTb8T.dRTMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/ea769a44-c551-4f3a-bb77-5d2b567b8e77)
