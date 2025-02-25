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
mensagem no terminal
```
overflow me:
```
usando o `gdb bof`

![image](https://github.com/user-attachments/assets/5b512de4-7913-422a-b98c-7cd8de8763f8)

![image](https://github.com/user-attachments/assets/87002075-b9ee-480a-87f3-66004b97b4cb)

![image](https://github.com/user-attachments/assets/2cdbc4b0-72ca-4961-8dd5-2c87fb074e79)

![image](https://github.com/user-attachments/assets/ee88ab18-e00f-4340-86f1-d2f4d14891d6)

![image](https://github.com/user-attachments/assets/825153ba-a22c-4098-80a4-32a08527067d)

![image](https://github.com/user-attachments/assets/56d511d8-946a-4f09-a892-322d0b9b83e1)



> Assim, obtemos a flag `picoCTF{y0u_m4d3_1t_cab35b843fdd6bd889f76566c6279114}`  

