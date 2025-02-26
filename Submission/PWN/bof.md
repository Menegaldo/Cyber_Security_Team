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

O primeiro passo para executar esse desafio é baixar tanto o executável `bof` quanto o código-fonte `.c`. Com isso, é possível analisar o código em C para compreender seu funcionamento e direcionar os esforços corretamente.

```c
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

Após a análise do código, observa-se que o objetivo é alcançar a linha `system("/bin/sh")`, permitindo o acesso a uma interface onde possivelmente estará a flag. Para atingir esse ponto, a variável `key` deve conter o valor `0xcafebabe`. Como a função `gets` é vulnerável a buffer overflow, pode-se explorar essa vulnerabilidade para sobrescrever a memória. No entanto, antes de qualquer ação, é necessário mapear a stack para inserir as informações no local correto.

O primeiro passo é conceder permissão de execução ao `bof` com o seguinte comando:

```
chmod +x bof
```

Em seguida, executa-se o programa:

```
./bof
```

Para analisar a estrutura da memória, utiliza-se a ferramenta `gdb` com o comando:

![image](https://github.com/user-attachments/assets/5b512de4-7913-422a-b98c-7cd8de8763f8)

Para verificar se a vulnerabilidade de buffer overflow de fato existe, pode-se inserir uma entrada maior que os 32 bytes da variável `overflowme`, somados aos 8 bytes do base pointer. Dessa forma, é possível atingir alguma área específica da memória e observar eventuais erros.

![image](https://github.com/user-attachments/assets/65f5b85a-fead-47d0-86ec-a960541f9e53)

O software GDB acusou um erro denominado `stack smashing detected`, indicando que a stack foi corrompida. Essa informação confirma que o código é vulnerável a buffer overflow. A partir desse ponto, a análise pode prosseguir para localizar as informações que precisam ser manipuladas na memória.

Utiliza-se o comando `(gdb) disas <função_analisada>` para analisar as funções `main` e `func`:

![image](https://github.com/user-attachments/assets/825153ba-a22c-4098-80a4-32a08527067d)

Na `main`, observa-se que o valor `0xdeadbeef` é passado para a função `func` no momento da chamada.

![image](https://github.com/user-attachments/assets/56d511d8-946a-4f09-a892-322d0b9b83e1)

Já na `func`, identifica-se o valor `0xcafebabe`, que precisa ser inserido na variável key para acessar `system("/bin/sh")`. Com essas informações em mãos, o próximo passo é interromper a execução quando a função `gets` for chamada, utilizando o comando `break gets`.
Após definir o breakpoint, executa-se o código com `run`. O programa aguardará a entrada do usuário ao alcançar `gets`. Ao inserir `next` e prosseguir com um valor, é possível identificar o endereço de memória onde a execução continua.

![image](https://github.com/user-attachments/assets/1ae2097a-81c9-464a-b5cc-b145bc0da2f6)

Em seguida, confirma-se a localização dos dados em `ebp+8` e `ebp-0x2c`, onde a entrada do usuário foi armazenada.

![image](https://github.com/user-attachments/assets/7c85f508-dec2-4a84-8b9b-7652f7c3a3ed)

Com esses valores, calcula-se a distância entre eles para determinar quantos bytes precisam ser escritos até atingir a variável `key` e realizar a comparação desejada.

![image](https://github.com/user-attachments/assets/1f67f213-4ef6-46db-8cbf-85da81d03e7a)

Agora, com todas as informações necessárias, basta executar o comando que insere 52 bytes de padding (`NOPs`) seguidos pelo valor `0xcafebabe` na variável `key`, garantindo o acesso à `system("/bin/sh")`.

```(python -c "print '\x90'*52 + '\xbe\xba\xfe\xca'"; cat) | nc pwnable.kr 9000```

Por fim, executa-se o comando para capturar a flag (`****`):

```
cat flag
```
> Assim, obtemos a flag `picoCTF{daddy, I just pwned a buFFer :)}`


<!--

TIRAR CANARY
```
gcc -fno-stack-protector -z execstack -no-pie -o bof bof.c
```

![image](https://github.com/user-attachments/assets/50d458d7-5525-4248-b460-442361f6351f)

![image](https://github.com/user-attachments/assets/c18595e5-ba5a-4f99-9f7b-21f4e246069d)

![image](https://github.com/user-attachments/assets/cdf1fbca-ef25-49ba-bb03-881f63d7157a)
 
 -->



