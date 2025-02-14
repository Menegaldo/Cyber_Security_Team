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

Verificando o código da página temos esse código HTML.<br>

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

Colocando qualquer valor no campo e fazendo a submissão.<br>

![image](https://github.com/user-attachments/assets/f29b64a1-451c-4a4f-bdbf-38ed2ea0df45)

Abrindo o site no BurpSuite, conseguimos ver que recebemos uma string de volta em decimal, no entanto, em um intervalo de 0 a 255, portanto, ele estão representando binário, aparentemente, quando fazemos uma pesquisa no campo.<br>

![image](https://github.com/user-attachments/assets/ed85f336-ba44-4c9b-8254-2682bfce9cff)

```
87 130 78 188 0 84 26 157 143 239 249 82 248 212 239 82 195 80 1 207 13 6 1 0 119 243 73 193 78 36 133 108 85 0 0 14 0 186 68 0 0 222 0 243 0 24 174 163 126 0 133 252 137 177 121 10 0 0 0 237 73 63 0 100 96 20 3 224 59 171 16 114 0 0 0 69 0 68 68 147 137 179 110 112 74 121 238 65 1 0 156 0 155 0 95 120 0 233 226 40 78 194 248 44 84 0 208 13 41 72 138 59 164 98 71 0 209 0 99 176 97 120 202 0 135 192 54 101 64 252 81 71 205 10 243 133 30 22 125 237 3 93 90 42 73 221 25 114 243 0 116 22 4 3 59 75 188 119 169 221 161 184 178 2 73 73 231 45 14 99 102 153 166 178 206 54 127 84 240 191 220 10 163 81 64 206 128 132 102 197 72 127 239 253 78 93 8 22 239 207 146 111 143 239 27 243 28 0 173 159 196 48 247 28 84 98 63 52 171 214 214 26 233 254 65 106 111 59 73 255 148 111 103 91 20 206 222 70 252 199 161 124 245 188 102 81 159 119 174 51 190 243 55 243 156 249 124 125 2 143 191 27 119 139 126 88 18 247 171 227 72 66 54 251 0 80 171 146 113 173 4 79 211 216 214 122 119 115 225 45 24 54 44 76 43 253 5 235 104 248 96 8 229 200 75 64 233 217 23 87 40 254 187 107 181 200 181 233 181 81 231 171 165 82 254 196 239 51 43 114 170 73 249 50 114 201 138 64 11 203 155 192 249 226 35 188 156 223 40 217 67 75 100 45 93 102 169 13 34 197 80 175 210 128 137 201 167 45 140 82 171 56 212 17 126 113 139 229 127 223 181 15 0 116 221 186 219 230 56 233 31 15 249 74 119 152 44 41 226 60 35 253 172 97 32 137 233 165 35 181 104 80 217 56 186 205 212 15 64 81 230 230 153 62 251 251 47 151 141 108 32 25 65 11 253 119 201 147 243 11 31 247 233 54 126 217 136 141 191 226 137 213 131 239 100 145 151 150 119 124 159 203 190 63 18 170 210 175 122 223 223 114 124 59 93 245 177 100 15 57 63 239 165 144 13 149 32 198 39 52 53 113 97 91 186 76 91 74 207 133 208 0 245 241 245 73 122 193 223 159 82 175 241 159 231 205 24 92 75 11 247 77 55 170 7 95 127 143 96 207 242 142 153 226 242 93 163 110 185 26 188 4 178 102 159 97 53 58 186 172 239 6 78 215 65 156 90 150 112 205 73 76 149 163 159 242 45 147 16 210 49 254 82 126 200 30 62 190 230 2 86 171 181 197 185 132 170 153 82 191 154 235 147 55 57 92 252 48 207 118 191 170 253 53 127 94 143 122 230 254 154 151 186 55 160 132 126 57 183 217 129 181 95 255 35 223 50 70 77 107 100 203 17 61 163 17 227 147 182 184 79 126 239 28 115 159 254 111 90 250 14 206 185 137 187 141 231 211 241 249 39 99 131 95 210 50 147 241 95 127 103 239 113 165 223 164 245 35 231 132 166 220 241 207 67 178 148 29 156 94 194 74 222 110 0 243 107 158 173 214 210 249 84 66 107 40 0 203 138 164 0 241 9 109 147 207 85 29 204 0
```

Sabemos pelo código da página em HTML que a KEY tem tamanho de 16 bytes, já sabendo que os primeiros 8 bytes são os 'Bytes mágicos' que contém PNG ```0x89 0x50 0x4E 0x47 0x0D 0x0A 0x1A 0x0A```, e os outros 8 bytes descobrimos dando um hexdump no arquivo PNG, sendo eles ```0x00 0x00 0x00 0x0D 0x49 0x48 0x44 0x5```.

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
