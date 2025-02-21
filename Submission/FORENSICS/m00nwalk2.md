# M00nwalk2
###### Resolvido por @Menegaldo
> Este é um CTF sobre []  

## Sobre o Desafio  
Revisite a última transmissão. Achamos que esta transmissão contém uma mensagem oculta.
Existem também algumas pistas: pista 1, pista 2, pista 3.

## 🔗 Links do Desafio

[m00nwalk2](https://play.picoctf.org/practice/challenge/28) <br>
[Pista 1](https://jupiter.challenges.picoctf.org/static/599404f0bf7426a5a5c2deb538860cda/clue1.wav)<br>
[Pista 2](https://jupiter.challenges.picoctf.org/static/599404f0bf7426a5a5c2deb538860cda/clue2.wav)<br>
[Pista 3](https://jupiter.challenges.picoctf.org/static/599404f0bf7426a5a5c2deb538860cda/clue3.wav)<br>
[transmissão](https://jupiter.challenges.picoctf.org/static/599404f0bf7426a5a5c2deb538860cda/message.wav)

## Solução

O enunciado entrega 4 arquivo .wav, e disse que deve-se revistar a última transmissão. essa transmissão foi realizada no formato sstv, que tem como objetivo transmitir informações (imagem) em cima de espectro de frequência, passando então os 3 arquivos de pista pelo qsstv (decodificador) recebemos a imagem 1.

![clue1](https://github.com/user-attachments/assets/2886fc86-3180-40ae-90f2-ff41c12a0543)

Essa imagem contém algumas informações, como o seguinte texto: "password hidden_stegosaurus" com um desenho de dinossauro a baixo.

![clue2](https://github.com/user-attachments/assets/9f6113a7-3979-4c5a-ae03-209f473087f3)

A segunda imagem decodificada tem o seguinte texto "The quieter you are the more you can HEAR" com o desenho de duas orelhas.

![clue3](https://github.com/user-attachments/assets/adc0f600-3438-4970-9181-36f520a1ebc8)

Por fim, a ultima tem a seguinte frase "Alan Eliasen the FutureBoy".

Pesquisando sobre esse trecho "Alan Eliasen the FutureBoy", chegamos até esse site:

`https://futureboy.us/`

Pesquisando dentro do site, achamos esse uma ferramenta `Steganography Tools` nessa URL `https://futureboy.us/stegano/`.

Juntando o arquivo denominado `message.wav` e a senha que estava na primeira imagem, decodificamos e chegamos até essa página `https://futureboy.us/stegano/decode.pl`.

![image](https://github.com/user-attachments/assets/8e9dad87-a408-49cb-a3a9-a9781083f54d)

> Assim, obtemos a flag `picoCTF{the_answer_lies_hidden_in_plain_sight}`
