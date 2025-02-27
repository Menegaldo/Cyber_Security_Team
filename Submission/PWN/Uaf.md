
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


Quando um objeto chama ```introduce()```, na verdade, ele está acessando o endereço armazenado na vtable. Ao procurar o endereço de memória da função ```introduce```, encontrei-o na posição ```0x401578```.

Neste ponto, fica claro o que precisamos fazer: alocar um espaço de memória usando a opção after para simular um objeto Man real, mas com o ponteiro da vtable modificado. Dessa forma, ao usar a opção use, em vez de chamar introduce, a função give_shell será executada.
![image](https://github.com/user-attachments/assets/94bc2949-ec58-44ae-9a6a-f4d42e156662)



E procurando os endereços de memória para achar onde está a função give_shell:
![image](https://github.com/user-attachments/assets/4b496240-1bf8-4a96-b494-6dcd2e86f397)

Percebemos que seu endereço de memória é ```0040117a``` 

Ao subtrair os 2 endereços para sabermos quantos bytes há no meio deles, recebemos 18 bytes de diferença.
![image](https://github.com/user-attachments/assets/f284c95d-e2a5-4a4b-9c54-237b3327f579)

python -c 'print ("\80\x11\x40\x00" + "\x00"*18)' > /tmp/uaf-poc



>`yay_f1ag_aft3r_pwning`
