```py
from pwn import asm, context, process

context.update(arch='amd64', os='linux')

shellcode = asm("""
    mov ebx, [rdi+4]
    mov ecx, [rdi+8]
    mov edx, [rdi+12]
    mov eax, [rdi]

    cmp eax, 0x7f454c46
    je case1

    cmp eax, 0x00005A4D
    je case2

    imul ebx, ecx
    imul ebx, edx
    jmp done

case1:
    add ebx, ecx
    add ebx, edx
    jmp done

case2:
    sub ebx, ecx
    sub ebx, edx
    jmp done

done:
    mov eax, ebx
""")

p = process('/challenge/run')
p.send(shellcode)
print(p.recvall().decode())
```

pwn.college{wOxwJcwR21tYbp1OR0Se4qThKmW.dFTMywyMyITMyEzW}

![image](https://github.com/user-attachments/assets/53eaeb74-a915-402b-909f-a965325733ea)


