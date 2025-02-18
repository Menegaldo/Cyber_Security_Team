# Matryoshka doll
###### Resolvido por @Menegaldo
> Este é um CTF sobre []  

## Sobre o Desafio  
As bonecas Matryoshka são um conjunto de bonecas de madeira de tamanho decrescente colocadas umas dentro das outras. Qual é o último? 

## 🔗 Links do Desafio

[Matryoshka doll](https://play.picoctf.org/practice/challenge/129) <br>
[Imagem](https://mercury.picoctf.net/static/1b70cffdd2f05427fff97d13c496963f/dolls.jpg)

## Solução  

O título do exercício já nos dá uma pista: a **Matryoshka doll** (boneca russa) é caracterizada por conter uma boneca dentro da outra, sempre em tamanhos menores. O enunciado nos fornece apenas um arquivo **.jpg** com essa boneca, o que sugere que podemos estar lidando com um arquivo dentro de outro, de forma análoga ao conceito da Matryoshka.  

### Análise Inicial  

Utilizando o comando:  

```bash
exiftool dolls.jpg
```  

Verificamos que, embora o nome do arquivo seja **dolls.jpg**, ele foi identificado como um **PNG**, o que indica que houve uma alteração de formato.  

![image](https://github.com/user-attachments/assets/dd32e48a-62d5-4095-8408-3ce79193340c)  

Além disso, o campo **"User Comment"** contém a palavra **"Screenshot"**, sugerindo que pode ser uma captura de tela.  

![image](https://github.com/user-attachments/assets/3da2866c-2fd1-4fa6-a279-dad5b5bab759)  

Outro detalhe importante é o aviso **"Trailer data after PNG IEND chunk"**, que indica a presença de dados extras após o final do arquivo PNG—um forte indício de esteganografia.  

![image](https://github.com/user-attachments/assets/b5eaa302-e04b-408f-a687-7e614882454f)  

### Investigação com Binwalk  

Para uma análise mais profunda, utilizamos o comando:  

```bash
binwalk dolls.jpg
```  

O resultado revelou que o arquivo é, de fato, um **PNG**. Além disso, encontramos dados de uma **imagem TIFF** logo após o PNG, o que pode indicar um arquivo embutido ou modificado.  

![image](https://github.com/user-attachments/assets/7f5eb1d1-60bc-4dc3-9950-950506639f1b)  

O dado mais relevante, no entanto, é um **arquivo ZIP** encontrado no **offset 272492**. Esse ZIP contém um arquivo chamado **"base_images/2_c.jpg"**, sugerindo que há outra imagem JPEG oculta dentro do arquivo original.  

Além disso, o **fim do arquivo ZIP** também foi identificado, confirmando que há um arquivo comprimido completo dentro da imagem.  


-------------

Use o comando dd para extrair o conteúdo do arquivo zip a partir dessa posição: dd if=dolls.jpg of=archive.zip bs=1 skip=272492

Use o comando unzip para extrair o conteúdo: unzip archive.zip -d extracted_files

-------------
imagem 2

Os metadados da imagem 2_c.jpg revelam alguns pontos interessantes:

Formato PNG Identificado: Embora o arquivo tenha a extensão .jpg, ele é identificado como um PNG. Isso pode indicar que o arquivo foi renomeado para se passar por um arquivo JPEG, mas na realidade é um arquivo PNG modificado.

Comentário de Usuário: O campo "User Comment" menciona "Screenshot", o que pode ser uma pista adicional, sugerindo que o arquivo é de fato uma captura de tela ou algo relacionado a um conteúdo oculto.

Aviso de Dados após o Chunk PNG IEND: O aviso sobre dados após o IEND (o final do arquivo PNG) é muito relevante, pois indica que há dados extras após o final da imagem PNG. Esse tipo de dado extra frequentemente é usado em técnicas de esteganografia, ou para esconder informações dentro do arquivo.

-----------

Parece que o comando binwalk encontrou mais dados interessantes dentro do arquivo:

Arquivo Zip (base_images/3_c.jpg): A partir do byte 187707 (em hexadecimal 0x2DD3B), há novamente um arquivo ZIP oculto dentro da imagem. Este arquivo zip contém um arquivo JPEG chamado base_images/3_c.jpg.

Fim do Arquivo Zip: O binwalk também detectou o fim do arquivo zip nas posições 383805 e 383916, indicando que o arquivo zip está completo e pronto para ser extraído.

-----------
imagem 3

A análise da imagem 3_c.jpg revela que ela é identificada como um arquivo PNG, com a presença de um perfil de cores ICC, além de dados extras como curvas de reprodução de cores e informações específicas de dispositivos Apple. Também temos algumas observações importantes:

Aviso de Trailer após o Chunk IEND: Isso pode indicar que há dados extras depois do final do arquivo PNG, o que pode sugerir a presença de esteganografia ou outra camada de dados.
Usuário comentou como "Screenshot": Um comentário simples, mas que pode ser relevante dependendo do contexto.

-----------
imagem 4

![image](https://github.com/user-attachments/assets/cc956a7b-7ad0-45d5-9160-c4451948ec65)

![image](https://github.com/user-attachments/assets/0b899b93-5a24-4d64-b672-551674e61fcc)

> Assim, obtemos a flag `picoCTF{bf6acf878dcbd752f4721e41b1b1b66b} `  

