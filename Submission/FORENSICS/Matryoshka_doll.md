# Matryoshka doll
###### Resolvido por @Menegaldo e @BrunoCuba 
> Este é um CTF sobre []  

## Sobre o Desafio  
As bonecas Matryoshka são um conjunto de bonecas de madeira de tamanho decrescente colocadas umas dentro das outras. Qual é o último? 

## 🔗 Links do Desafio

[Matryoshka doll](https://play.picoctf.org/practice/challenge/129) <br>
[Imagem](https://mercury.picoctf.net/static/1b70cffdd2f05427fff97d13c496963f/dolls.jpg)

Boa sorte! 🔥

## Solução

Usando o ```exiftool dolls.jpg```

1️⃣ O arquivo foi identificado como PNG, mas o nome é "dolls.jpg", o que pode ser uma pista de que houve alguma modificação no formato.<br>

![image](https://github.com/user-attachments/assets/dd32e48a-62d5-4095-8408-3ce79193340c)

2️⃣ O campo "User Comment" diz "Screenshot", o que sugere que pode ser uma captura de tela.<br>

![image](https://github.com/user-attachments/assets/3da2866c-2fd1-4fa6-a279-dad5b5bab759)

3️⃣ O aviso "Trailer data after PNG IEND chunk" indica que há dados extras após o final do arquivo, o que é um forte indício de esteganografia.<br>

![image](https://github.com/user-attachments/assets/b5eaa302-e04b-408f-a687-7e614882454f)

--------------

Usando o ```binwalk dolls.jpg ```

Imagem PNG: A imagem começa com um arquivo PNG válido, o que parece ser a parte visível do arquivo.

TIFF Image: O binwalk encontrou dados relacionados a uma imagem TIFF logo após o PNG. Isso pode ser parte de um arquivo embutido ou modificado.

Zip Archive: O dado mais importante é o Zip Archive encontrado no byte 272492. Este arquivo comprimido tem o nome base_images/2_c.jpg, sugerindo que dentro da imagem pode haver outro arquivo JPEG oculto.

Fim do Zip Archive: O binwalk também identificou o fim do arquivo zip, indicando que há um arquivo zip completo dentro da imagem.

![image](https://github.com/user-attachments/assets/7f5eb1d1-60bc-4dc3-9950-950506639f1b)

-------------







> Assim, obtemos a flag `picoCTF{y0u_m4d3_1t_cab35b843fdd6bd889f76566c6279114}`  

