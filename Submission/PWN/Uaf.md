
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

Descompilando o código no ghidra precisamos procurar duas funções importantes no código a ```introduce``` e a ```give_shell```, pois na lógica do código, ao trocarmos a informação que adicionamos na opção 1 ```use``` conseguimos fazer o ponteiro que não estaria alocado para a função ```introduce``` e faríamos ele apontar para a função do ```give shell```.
Ao procurar o endereço de memória do ```introduce``` achei ele na posição ```401192```
![image](https://github.com/user-attachments/assets/1b710ddb-ff4b-489c-82b1-0909a9d96fca)



E procurando os endereços de memória para achar onde está a função give_shell:
![image](https://github.com/user-attachments/assets/4b496240-1bf8-4a96-b494-6dcd2e86f397)

Percebemos que seu endereço de memória é ```0040117a``` 

>`[Insira a flag]`
