# M00nwalk2
###### Resolvido por @Menegaldo
> Este é um CTF sobre [Plaintext Password Exposure, Lack of Encryption in Communication, Weak Steganographic Protection, Dependence on Public Resources, Exposure of Metadata and Sensitive Information, Engineering Risks and Predictability and Social]

## Sobre o Desafio  
Revisite a última transmissão. Achamos que esta transmissão contém uma mensagem oculta.
Existem também algumas pistas: pista 1, pista 2, pista 3.

## Links do Desafio

[m00nwalk2](https://play.picoctf.org/practice/challenge/28) <br>
[Pista 1](https://jupiter.challenges.picoctf.org/static/599404f0bf7426a5a5c2deb538860cda/clue1.wav)<br>
[Pista 2](https://jupiter.challenges.picoctf.org/static/599404f0bf7426a5a5c2deb538860cda/clue2.wav)<br>
[Pista 3](https://jupiter.challenges.picoctf.org/static/599404f0bf7426a5a5c2deb538860cda/clue3.wav)<br>
[transmissão](https://jupiter.challenges.picoctf.org/static/599404f0bf7426a5a5c2deb538860cda/message.wav)

## Solução

O enunciado fornece quatro arquivos no formato .wav e instrui a revisar a última transmissão. A análise revela que essa transmissão foi realizada no formato SSTV (Slow Scan Television), utilizado para transmitir imagens por meio do espectro de frequência.

Ao processar três dos arquivos pelo software QSSTV (decodificador SSTV), obteve-se a seguinte imagem:

![clue1](https://github.com/user-attachments/assets/7b25fd37-96b3-447c-a3a4-1294772ad48b)

Essa imagem contém algumas informações, incluindo o texto `password hidden_stegosaurus`, acompanhado de um desenho de dinossauro.

![image](https://github.com/user-attachments/assets/21ce3b6f-98b2-461c-b5d7-a5435664ff3b)

A segunda imagem decodificada apresenta o texto `The quieter you are the more you can HEAR`, juntamente com um desenho de duas orelhas.

![image](https://github.com/user-attachments/assets/d58e8413-9003-4ece-bf2c-3c6943b6fc2a)

Por fim, a última imagem contém a seguinte frase: `Alan Eliasen the FutureBoy`.

Pesquisando sobre "Alan Eliasen the FutureBoy", encontrou-se o site:

`https://futureboy.us/`

Dentro desse site, foi localizada uma ferramenta de esteganografia (Steganography Tools), acessível na seguinte URL:

`https://futureboy.us/stegano/`

Combinando o arquivo denominado message.wav com a senha obtida na primeira imagem (hidden_stegosaurus), foi possível decodificar o conteúdo e acessar a seguinte página:

`https://futureboy.us/stegano/decode.pl`

![image](https://github.com/user-attachments/assets/8e9dad87-a408-49cb-a3a9-a9781083f54d)

> Assim, foi obtida a flag `picoCTF{the_answer_lies_hidden_in_plain_sight}`
