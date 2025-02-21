# endianness-v2
###### Resolvido por @Cubano2
> Este é um CTF sobre [Incorrect File Extensions, browser_webshell_solvable, Forensic Data Extraction]

## Sobre o Desafio
Aqui está um arquivo que foi recuperado de um sistema de 32 bits que organizou os bytes de uma maneira estranha. Nem estamos certos de que tipo de arquivo é.

## Link do Desafio
[Endianness-v2](https://play.picoctf.org/practice/challenge/415) <br>

## Solução  

O exercício começa com o download de um arquivo cujo tipo é desconhecido. A única dica fornecida pelo exercício é que os bytes estão organizados de uma maneira 'esquisita'.

Ao solicitar ajuda para analisar o arquivo ao GPT, ele leu todo o seu código em `HEX` e apontou uma possível direção: o arquivo poderia ser um JPEG devido aos bytes mágicos encontrados no início do arquivo: `\xff\xd8\xff`.

![image](https://github.com/user-attachments/assets/f1ef58a5-a273-404c-adf6-0726157337e3)

Com a suspeita de que se tratava de uma imagem JPEG, foi realizada uma tentativa de renomear o arquivo adicionando a extensão .jpeg para verificar se a imagem seria gerada corretamente. No entanto, isso não produziu nenhum resultado.

Sabendo que a informação do arquivo estava embaralhada, foi realizada uma pesquisa sobre programas que possibilitassem a leitura do código em hexadecimal para confirmar se o arquivo realmente era um JPEG, conforme sugerido pelo GPT. Como resultado, foi recomendado o uso do `ghex` no Kali Linux.

Ao abrir o arquivo Challengefile no ghex, foi possível visualizar um grande conjunto de bytes.

![image](https://github.com/user-attachments/assets/5d31f29b-2b7c-4afa-9905-cfff81e401e7)

Com base nas informações fornecidas pelo GPT, foi confirmado que o arquivo poderia ser uma imagem JPEG, porém os bytes mágicos do formato estavam invertidos. Para um arquivo JPEG válido, a ordem correta seria `FF D8 FF E0`, mas no arquivo analisado, a ordem encontrada foi `E0 FF D8 FF`.

Ao comentar sobre essa inversão com o GPT, foi sugerido que os bytes poderiam estar organizados no formato Little Endian, ou seja, do byte menos significativo para o mais significativo, em blocos de 4 em 4 posições. Dessa forma, ao converter de Little Endian para Big Endian, o arquivo possivelmente retornaria à sua forma original.

![image](https://github.com/user-attachments/assets/9013f3b6-8e9e-433e-ad93-747756d01a3d)

Com essa informação, o GPT recomendou o seguinte comando para ser executado no terminal:

`hexdump -v -e '1/4 "%08x"' -e '"\n"' challengefile | xxd -r -p > reconstructed_image.jpg`

![image](https://github.com/user-attachments/assets/18368921-098c-4b21-8370-ff060d704e4f)

![image](https://github.com/user-attachments/assets/9e1cf0fe-7db7-4da2-800b-dc4e5b3a192a)

> Assim, foi obtida a flag `picoCTF{cert!f1Ed_iNd!4n_s0rrY_3nDian_94cc03f3}`
