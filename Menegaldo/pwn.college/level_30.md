```py
from pwn import *

context.update(arch='amd64')

code = asm("""
    mov rbp, rsp
    sub rsp, 0x100
    xor r8, r8

most_common_byte:
loop1:
    cmp r8, rsi
    jg next
    mov dl, byte ptr [rdi + r8]
    add byte ptr [rsp + rdx], 1
    inc r8
    jmp loop1

next:
    xor rcx, rcx
    xor rbx, rbx
    xor rax, rax

loop2:
    cmp rcx, 0xff
    jg done
    cmp [rsp + rcx], bl
    jg updateFreq
    jmp increment

updateFreq:
    mov bl, [rsp + rcx]
    mov rax, rcx

increment:
    inc rcx
    jmp loop2

done:
    mov rsp, rbp
    ret
""")

p = process('/challenge/run')
p.write(code)
print(p.readall().decode())
```

pwn.college{QFIlhc8bXZUvR1iQ_B5e2rxL4r2.dZTMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/70072909-6fc3-4b4b-bcff-038ec251dbff)
