# endianness-v2
###### Resolvido por @Cubano2
> Este é um CTF sobre [Incorrect File Extensions, browser_webshell_solvable, Forensic Data Extraction]

## Sobre o Desafio
Aqui está um arquivo que foi recuperado de um sistema de 32 bits que organizou os bytes de uma maneira estranha. Nem estamos certos de que tipo de arquivo é.

## 🔗 Link do Desafio
[Endianness-v2](https://play.picoctf.org/practice/challenge/415) <br>

## Solução  

O exercício começa ao baixarmos um arquivo em qual não sabemos o tipo de arquivo que ele é. A única dica que o exercício nos dá é que os bytes estão organizados de uma maneira de uma maneira 'esquisita'.

Pedindo ajuda para analisar o arquivo pro GPT, ele leu todo seu código em ```HEX``` e já me apontou em uma direção que o arquivo possivelmente seria um arquivo JPEG por conta dos Bytes mágicos no começo do arquivo: ```\xff\xd8\xff```.
![image](https://github.com/user-attachments/assets/f1ef58a5-a273-404c-adf6-0726157337e3)

Pensando que seria uma imagem JPEG, por só uma tentativa, tentei colocar .jpeg no final do arquivo para ver se me gerava a imagem, porém não resultou em nada.

Sabendo que a informação do arquivo estava embaralhada pesquisei um programa para que eu conseguisse ler o código em Hexadecimal e ver se o arquivo realmente é  um JPEG de acordo com o GPT. Ele me recomendou um pra usar no Kali Linux chamada ghex.

Abrindo o Challengefile no ghex somos apresentados à esse grande conjunto de bytes.
![image](https://github.com/user-attachments/assets/5d31f29b-2b7c-4afa-9905-cfff81e401e7)
E com a informação que o GPT me deu esse arquivo realmente pode ser uma imagem JPEG, porém os Bytes mágicos do JPEG estão ao contrário. Se fosse uma imagem JPEG a ordem correta deles seria ```FF D8 FF E0 ``` mas no arquivo está ``` E0 FF D8 FF ```.


Ao comentar isso com o GPT ele me falou que os bytes podem estar em Little Endian, isso é, do Byte menos significativo para o mais significativo, de 4 em 4 posições, se convertessemos de Little para Big Endian, o arquivo possivelmente voltaria para sua forma normal.
![image](https://github.com/user-attachments/assets/a0a7c2bc-2057-48c1-bcce-a5dc4667d4db)



Com isso tudo ele me recomendou esse seguinte comando para jogar no terminal.

``` hexdump -v -e '1/4 "%08x"' -e '"\n"' challengefile | xxd -r -p > reconstructed_image.jpg ```
![image](https://github.com/user-attachments/assets/18368921-098c-4b21-8370-ff060d704e4f)
![image](https://github.com/user-attachments/assets/9e1cf0fe-7db7-4da2-800b-dc4e5b3a192a)

> Assim, obtemos a flag `picoCTF{cert!f1Ed_iNd!4n_s0rrY_3nDian_94cc03f3} `
