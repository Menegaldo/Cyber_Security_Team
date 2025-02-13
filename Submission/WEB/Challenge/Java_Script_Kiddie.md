# Java Script Kiddie  
###### Resolvido por @Menegaldo e @BrunoCuba  
> Este é um CTF sobre [Tipo de Vulnerabilidade 1, Tipo de Vulnerabilidade 2 e Tipo de Vulnerabilidade 3]  

## Sobre o Desafio

The image link appears broken...

## 🔗 Links do Desafio
- [Java_Script_Kiddie](https://play.picoctf.org/practice/challenge/29)
- [Acesse o desafio - Link 1](https://jupiter.challenges.picoctf.org/problem/17205)  
- [Acesse o desafio - Link 2](http://jupiter.challenges.picoctf.org:17205)  

## Solução  

Quando colocar qualquer valor no campo e faz a submissão, recebe um string de volta:<br>
![image](https://github.com/user-attachments/assets/f29b64a1-451c-4a4f-bdbf-38ed2ea0df45)

recebe um string hexadecimal de volta<br>
![image](https://github.com/user-attachments/assets/ed85f336-ba44-4c9b-8254-2682bfce9cff)

```kali
┌──(kali㉿kali)-[~]
└─$ python3 cod.py

Key 5108180323263640 produces an invalid PNG - Ignoring
Key 5108180323363640 produces an invalid PNG - Ignoring
Key 5108180323463640 produces an invalid PNG - Ignoring
Key 5108180324263640 produces an invalid PNG - Ignoring
Key 5108180324363640 produces an invalid PNG - Ignoring
Key 5108180324463640 produces an invalid PNG - Ignoring
Key 5108180325263640 produces an invalid PNG - Ignoring
Key 5108180325363640 produces an invalid PNG - Ignoring
Key 5108180325463640 produces an invalid PNG - Ignoring
Key 5108180326263640 produces an invalid PNG - Ignoring
Key 5108180326363640 produces an invalid PNG - Ignoring
Key 5108180326463640 produces an invalid PNG - Ignoring
Key 5108180333263640 produces an invalid PNG - Ignoring
Key 5108180333363640 produces an invalid PNG - Ignoring
Key 5108180333463640 produces an invalid PNG - Ignoring
Key 5108180334263640 produces an invalid PNG - Ignoring
Key 5108180334363640 produces an invalid PNG - Ignoring
Key 5108180334463640 produces an invalid PNG - Ignoring
Key 5108180335263640 produces an invalid PNG - Ignoring
Key 5108180335363640 produces an invalid PNG - Ignoring
Key 5108180335463640 produces an invalid PNG - Ignoring
Key 5108180336263640 produces an invalid PNG - Ignoring
Key 5108180336363640 produces an invalid PNG - Ignoring
Key 5108180336463640 produces an invalid PNG - Ignoring
Key 5108180343263640 produces an invalid PNG - Ignoring
Key 5108180343363640 produces an invalid PNG - Ignoring
Key 5108180343463640 produces an invalid PNG - Ignoring
Key 5108180344263640 produces an invalid PNG - Ignoring
Key 5108180344363640 produces an invalid PNG - Ignoring
Key 5108180344463640 produces an invalid PNG - Ignoring
Key 5108180345263640 produces an invalid PNG - Ignoring
Key 5108180345363640 produces a valid PNG - Saving
Key 5108180345463640 produces an invalid PNG - Ignoring
Key 5108180346263640 produces an invalid PNG - Ignoring
Key 5108180346363640 produces an invalid PNG - Ignoring
Key 5108180346463640 produces an invalid PNG - Ignoring

```
usando a ```Key 5108180345363640 produces a valid PNG - Saving``` como chave.

![image](https://github.com/user-attachments/assets/60493865-d9df-4669-a027-0418e84ad94b)

> Assim, obtemos a flag `picoCTF{066cad9e69c5c7e5d2784185c0feb30b}`  
