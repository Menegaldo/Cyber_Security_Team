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

Quando abrimos o site temos um quadrado para digitarmos e um botão de envio.

![image](https://github.com/user-attachments/assets/34ae5ac5-7ecf-4220-bbca-711ee1131487)

Verificando o código da página temos esse código HTML, vemos que o site está esperando uma entrada de 16 números, essa entrada quando realizada devolverá uma imagem PNG.<br>

```html
<html>
	<head>    
		<script src="jquery-3.3.1.min.js"></script>
		<script>
			var bytes = [];
			$.get("bytes", function(resp) {
				bytes = Array.from(resp.split(" "), x => Number(x));
			});

			function assemble_png(u_in){
				var LEN = 16;
				var key = "0000000000000000";
				var shifter;
				if(u_in.length == LEN){
					key = u_in;
				}
				var result = [];
				for(var i = 0; i < LEN; i++){
					shifter = key.charCodeAt(i) - 48;
					for(var j = 0; j < (bytes.length / LEN); j ++){
						result[(j * LEN) + i] = bytes[(((j + shifter) * LEN) % bytes.length) + i]
					}
				}
				while(result[result.length-1] == 0){
					result = result.slice(0,result.length-1);
				}
				document.getElementById("Area").src = "data:image/png;base64," + btoa(String.fromCharCode.apply(null, new Uint8Array(result)));
				return false;
			}
		</script>
	</head>
	<body>

		<center>
			<form action="#" onsubmit="assemble_png(document.getElementById('user_in').value)">
				<input type="text" id="user_in">
				<input type="submit" value="Submit">
			</form>
			<img id="Area" src=""/>
		</center>

	</body>
</html>
```

Colocando qualquer valor no campo e fazendo a submissão, recebemos um link quebrado.<br>

![image](https://github.com/user-attachments/assets/73efbbb3-48cb-4780-b548-3b0c71341e4e)

Abrindo o site no BurpSuite, conseguimos ver que recebemos uma string de volta em decimal, no entanto, em um intervalo de 0 a 255, portanto, eles estão representando binário.<br>

![image](https://github.com/user-attachments/assets/ed85f336-ba44-4c9b-8254-2682bfce9cff)

```
87 130 78 188 0 84 26 157 143 239 249 82 248 212 239 82 195 80 1 207 13 6 1 0 119 243 73 193 78 36 133 108 85 0 0 14 0 186 68 0 0 222 0 243 0 24 174 163 126 0 133 252 137 177 121 10 0 0 0 237 73 63 0 100 96 20 3 224 59 171 16 114 0 0 0 69 0 68 68 147 137 179 110 112 74 121 238 65 1 0 156 0 155 0 95 120 0 233 226 40 78 194 248 44 84 0 208 13 41 72 138 59 164 98 71 0 209 0 99 176 97 120 202 0 135 192 54 101 64 252 81 71 205 10 243 133 30 22 125 237 3 93 90 42 73 221 25 114 243 0 116 22 4 3 59 75 188 119 169 221 161 184 178 2 73 73 231 45 14 99 102 153 166 178 206 54 127 84 240 191 220 10 163 81 64 206 128 132 102 197 72 127 239 253 78 93 8 22 239 207 146 111 143 239 27 243 28 0 173 159 196 48 247 28 84 98 63 52 171 214 214 26 233 254 65 106 111 59 73 255 148 111 103 91 20 206 222 70 252 199 161 124 245 188 102 81 159 119 174 51 190 243 55 243 156 249 124 125 2 143 191 27 119 139 126 88 18 247 171 227 72 66 54 251 0 80 171 146 113 173 4 79 211 216 214 122 119 115 225 45 24 54 44 76 43 253 5 235 104 248 96 8 229 200 75 64 233 217 23 87 40 254 187 107 181 200 181 233 181 81 231 171 165 82 254 196 239 51 43 114 170 73 249 50 114 201 138 64 11 203 155 192 249 226 35 188 156 223 40 217 67 75 100 45 93 102 169 13 34 197 80 175 210 128 137 201 167 45 140 82 171 56 212 17 126 113 139 229 127 223 181 15 0 116 221 186 219 230 56 233 31 15 249 74 119 152 44 41 226 60 35 253 172 97 32 137 233 165 35 181 104 80 217 56 186 205 212 15 64 81 230 230 153 62 251 251 47 151 141 108 32 25 65 11 253 119 201 147 243 11 31 247 233 54 126 217 136 141 191 226 137 213 131 239 100 145 151 150 119 124 159 203 190 63 18 170 210 175 122 223 223 114 124 59 93 245 177 100 15 57 63 239 165 144 13 149 32 198 39 52 53 113 97 91 186 76 91 74 207 133 208 0 245 241 245 73 122 193 223 159 82 175 241 159 231 205 24 92 75 11 247 77 55 170 7 95 127 143 96 207 242 142 153 226 242 93 163 110 185 26 188 4 178 102 159 97 53 58 186 172 239 6 78 215 65 156 90 150 112 205 73 76 149 163 159 242 45 147 16 210 49 254 82 126 200 30 62 190 230 2 86 171 181 197 185 132 170 153 82 191 154 235 147 55 57 92 252 48 207 118 191 170 253 53 127 94 143 122 230 254 154 151 186 55 160 132 126 57 183 217 129 181 95 255 35 223 50 70 77 107 100 203 17 61 163 17 227 147 182 184 79 126 239 28 115 159 254 111 90 250 14 206 185 137 187 141 231 211 241 249 39 99 131 95 210 50 147 241 95 127 103 239 113 165 223 164 245 35 231 132 166 220 241 207 67 178 148 29 156 94 194 74 222 110 0 243 107 158 173 214 210 249 84 66 107 40 0 203 138 164 0 241 9 109 147 207 85 29 204 0
```

convertendo essa string para hexadecimal, chegamos nisso.<br>

```
57 82 4E BC 00 54 1A 9D 8F EF F9 52 F8 D4 EF 52 C3 50 01 CF 0D 06 01 00 77 F3 49 C1 4E 24 85 6C 55 00 00 0E 00 BA 44 00 00 DE 00 F3 00 18 AE A3 7E 00 85 FC 89 B1 79 0A 00 00 00 ED 49 3F 00 64 60 14 03 E0 3B AB 10 72 00 00 00 45 00 44 44 93 89 B3 6E 70 4A 79 EE 41 01 00 9C 00 9B 00 5F 78 00 E9 E2 28 4E C2 F8 2C 54 00 D0 0D 29 48 8A 3B A4 62 47 00 D1 00 63 B0 61 78 CA 00 87 C0 36 65 40 FC 51 47 CD 0A F3 85 1E 16 7D ED 03 5D 5A 2A 49 DD 19 72 F3 00 74 16 04 03 3B 4B BC 77 A9 DD A1 B8 B2 02 49 49 E7 2D 0E 63 66 99 A6 B2 CE 36 7F 54 F0 BF DC 0A A3 51 40 CE 80 84 66 C5 48 7F EF FD 4E 5D 08 16 EF CF 92 6F 8F EF 1B F3 1C 00 AD 9F C4 30 F7 1C 54 62 3F 34 AB D6 D6 1A E9 FE 41 6A 6F 3B 49 FF 94 6F 67 5B 14 CE DE 46 FC C7 A1 7C F5 BC 66 51 9F 77 AE 33 BE F3 37 F3 9C F9 7C 7D 02 8F BF 1B 77 8B 7E 58 12 F7 AB E3 48 42 36 FB 00 50 AB 92 71 AD 04 4F D3 D8 D6 7A 77 73 E1 2D 18 36 2C 4C 2B FD 05 EB 68 F8 60 08 E5 C8 4B 40 E9 D9 17 57 28 FE BB 6B B5 C8 B5 E9 B5 51 E7 AB A5 52 FE C4 EF 33 2B 72 AA 49 F9 32 72 C9 8A 40 0B CB 9B C0 F9 E2 23 BC 9C DF 28 D9 43 4B 64 2D 5D 66 A9 0D 22 C5 50 AF D2 80 89 C9 A7 2D 8C 52 AB 38 D4 11 7E 71 8B E5 7F DF B5 0F 00 74 DD BA DB E6 38 E9 1F 0F F9 4A 77 98 2C 29 E2 3C 23 FD AC 61 20 89 E9 A5 23 B5 68 50 D9 38 BA CD D4 0F 40 51 E6 E6 99 3E FB FB 2F 97 8D 6C 20 19 41 0B FD 77 C9 93 F3 0B 1F F7 E9 36 7E D9 88 8D BF E2 89 D5 83 EF 64 91 97 96 77 7C 9F CB BE 3F 12 AA D2 AF 7A DF DF 72 7C 3B 5D F5 B1 64 0F 39 3F EF A5 90 0D 95 20 C6 27 34 35 71 61 5B BA 4C 5B 4A CF 85 D0 00 F5 F1 F5 49 7A C1 DF 9F 52 AF F1 9F E7 CD 18 5C 4B 0B F7 4D 37 AA 07 5F 7F 8F 60 CF F2 8E 99 E2 F2 5D A3 6E B9 1A BC 04 B2 66 9F 61 35 3A BA AC EF 06 4E D7 41 9C 5A 96 70 CD 49 4C 95 A3 9F F2 2D 93 10 D2 31 FE 52 7E C8 1E 3E BE E6 02 56 AB B5 C5 B9 84 AA 99 52 BF 9A EB 93 37 39 5C FC 30 CF 76 BF AA FD 35 7F 5E 8F 7A E6 FE 9A 97 BA 37 A0 84 7E 39 B7 D9 81 B5 5F FF 23 DF 32 46 4D 6B 64 CB 11 3D A3 11 E3 93 B6 B8 4F 7E EF 1C 73 9F FE 6F 5A FA 0E CE B9 89 BB 8D E7 D3 F1 F9 27 63 83 5F D2 32 93 F1 5F 7F 67 EF 71 A5 DF A4 F5 23 E7 84 A6 DC F1 CF 43 B2 94 1D 9C 5E C2 4A DE 6E 00 F3 6B 9E AD D6 D2 F9 54 42 6B 28 00 CB 8A A4 00 F1 09 6D 93 CF 55 1D CC 00
```

Sabemos que um arquivo PNG tem os seus 16 primeiros bytes padronizados, sendo eles: ```0x89 0x50 0x4E 0x47 0x0D 0x0A 0x1A 0x0A 0x00 0x00 0x00 0x0D 0x49 0x48 0x44 0x52```.

```
89: Indica que o arquivo é binário (não um arquivo de texto).
50 4E 47: "PNG" (em ASCII).
0D 0A: Um par de caracteres de nova linha (CRLF).
1A 0A: Um caractere de controle que é usado para detectar arquivos truncados.
00 00 00 0D: O comprimento de 13 bytes para a próxima parte.
49 48 44 52: Início do bloco IHDR, que contém informações sobre a imagem (tamanho, cor, etc.).
```

Se organizarmos a string em grupos de 16 bytes, podemos ver e identificar um padrão.

![image](https://github.com/user-attachments/assets/4b17351a-e851-46e5-b97a-ff7d1415c444)

Se formos localizando os bytes que já conhecemos, vamos visualizar o padrão de deslocamento, olhando a posição do byte na coluna começando em 0, teremos a sequência ```5108180345363640```, que será a nossa chave.

Anteriormente, resolvemos por força bruta, dessa forma:

Usamos o código a seguir, que pegamos no link: ``` https://github.com/Dvd848/CTFs/blob/master/2019_picoCTF/Java_Script_Kiddie.md```, para tentar achar a key certa para gerar o PNG.

```python
from PIL import Image
import itertools, io, os
KEY_LEN = 16

def create_png(bytes_arr, key, out_dir_path):
    if not os.path.isdir(out_dir_path):
        raise Exception("Output folder ('{}') does not exist!".format(out_dir_path))
    result = [0] * len(bytes_arr)
    for i in range(KEY_LEN):
        shifter = int(key[i])
        for j in range(len(bytes_arr) // KEY_LEN):
            result[(j * KEY_LEN) + i] = bytes_arr[(((j + shifter) * KEY_LEN) % len(bytes_arr)) + i]
    img_bytes = io.BytesIO(bytes(result))

    try:
        img = Image.open(img_bytes)
        img.save(os.path.join(out_dir_path, "{}.png".format(key)))
        print ("Key {} produces a valid PNG - Saving".format(key))
    except IOError:
        print ("Key {} produces an invalid PNG - Ignoring".format(key))


shifters = []
for i in range(KEY_LEN):
    shifters.append([])
expected = [0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A, 0x00, 0x00, 0x00, 0x0D, 0x49, 0x48, 0x44, 0x52]
with open("bytes.txt") as f:
    bytes_arr = list(map(int, f.read().split(" ")))
    for i in range(KEY_LEN):
        for shifter in range(10):
            j = 0
            offset = (((j + shifter) * KEY_LEN) % len(bytes_arr)) + i
            if bytes_arr[offset] == expected[i]:
                shifters[i].append(shifter)               

for p in itertools.product(*shifters):
    key = "".join("{}".format(n) for n in p)
    create_png(bytes_arr, key, "out")
```

E jogando esse código como executável no Kali Linux, sendo esses os resultados:<br>

```
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

Vendo que a única KEY que gerou imagem foi essa:  ```Key 5108180345363640 produces a valid PNG - Saving```, e usando ela no site nos dá o seguinte qr code.

![image](https://github.com/user-attachments/assets/60493865-d9df-4669-a027-0418e84ad94b)

> Assim, obtemos a flag `picoCTF{066cad9e69c5c7e5d2784185c0feb30b}`


site: ```https://www.rapidtables.com/convert/number/ascii-hex-bin-dec-converter.html```
