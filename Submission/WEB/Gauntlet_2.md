# Gauntlet 2
###### Resolvido por @Menegaldo e @BrunoCuba 
> Este é um CTF sobre [Tipo de Vulnerabilidade 1, Tipo de Vulnerabilidade 2 e Tipo de Vulnerabilidade 3]  

## Sobre o Desafio  
This website looks familiar...  

**Objetivo:** Log in as admin Site:  

## 🔗 Links do Desafio  
- [Acesse o site](http://mercury.picoctf.net:21336/)  
- [Página de filtragem](http://mercury.picoctf.net:21336/filter.php)  

Boa sorte! 🔥  

## Solução  

Ao acessar o site com a segunda questão, percebi que ela seria quase igual ao web Gauntlet 1, por ter o mesmo esquema de uma página de Login e Senha.

![image](https://github.com/user-attachments/assets/286fe1ca-4336-46bd-80ef-d882e5392d3d)

Depois, acessei o `filter.php`, que no primeiro exercício mostrava quais eram os filtros para não usar no comando da Query. No caso deste exercício, os filtros são:

![image](https://github.com/user-attachments/assets/d59a122f-447c-4c72-a41b-3b0f023db98f)

Minhas primeiras tentativas incluíram buscar no ChatGPT por algumas alternativas, e ele me deu o seguinte comando:

```sql
LOGIN: admin' AND 1=1 AND (SELECT name FROM sqlite_master WHERE type='table') --  
SENHA: qualquer coisa
```

Porém, essa tentativa não foi favorável, pois o site tem um filtro de tamanho de caracteres também:

![image](https://github.com/user-attachments/assets/ae16e14f-c01e-477c-9b32-bec59c104f81)

Depois disso, pedi ao GPT para me passar todos os comandos do SQLite:

```sql
SELECT
FROM
WHERE
AND
OR
NOT
INSERT INTO
UPDATE
DELETE
CREATE TABLE
DROP TABLE
ALTER TABLE
JOIN
UNION
GROUP BY
HAVING
ORDER BY
LIMIT
DISTINCT
LIKE
IN
BETWEEN
IS NULL
IS NOT NULL
EXISTS
CASE
=
<
<=
<>
!=
IS TRUE
IS FALSE
IS NOT TRUE
IS NOT FALSE
IS
char()
hex()
substr()
length()
count()
sum()
avg()
min()
max()
lower()
upper()
random()
```

Com todas essas opções, comecei a tentar diferentes comandos de forma que o valor fosse positivo e nenhum filtro fosse barrado, mas nenhuma ideia funcionou. Pedi ajuda ao GPT novamente, e ele sugeriu tentar comparar dois números diferentes com o `IS NOT` para que o valor fosse `TRUE`, permitindo que a lógica no SQLite me desse acesso ao site.

Minha primeira tentativa foi:

```sql
LOGIN: 'qualquer coisa'
SENHA: 1' IS NOT '2
```

![image](https://github.com/user-attachments/assets/3e517e79-1815-433c-b75d-a382d167db5b)

![image](https://github.com/user-attachments/assets/fdbf8787-3af3-4c71-ac97-5ee47694a88e)

Eu percebi que não adiantaria tentar entrar com outro usuário, então voltei ao Web Gauntlet 1 para ver como o login do admin funcionava e tentar burlar o filtro.

No campo de LOGIN, eu precisava usar: `ad'||'min`, e então tentei novamente no campo de SENHA: `1' IS NOT '2`.

Com isso, consegui realizar o login no site.

![image](https://github.com/user-attachments/assets/1f2b11fe-7eae-40e4-b8dd-779e79cd8a86)  

![image](https://github.com/user-attachments/assets/5d4ccf40-2239-47dc-b20e-9391161df011)

Depois, acessei o `filter.php`, que me apresentou o seguinte código:

```php
<?php
session_start();

if (!isset($_SESSION["winner2"])) {
    $_SESSION["winner2"] = 0;
}
$win = $_SESSION["winner2"];
$view = ($_SERVER["PHP_SELF"] == "/filter.php");

if ($win === 0) {
    $filter = array("or", "and", "true", "false", "union", "like", "=", ">", "<", ";", "--", "/*", "*/", "admin");
    if ($view) {
        echo "Filters: ".implode(" ", $filter)."<br/>";
    }
} else if ($win === 1) {
    if ($view) {
        highlight_file("filter.php");
    }
    $_SESSION["winner2"] = 0;        // <- Don't refresh!
} else {
    $_SESSION["winner2"] = 0;
}

// picoCTF{0n3_m0r3_t1m3_e2db86ae880862ad471aa4c93343b2bf}
?>
```

> Assim, obtemos a flag: `picoCTF{0n3_m0r3_t1m3_e2db86ae880862ad471aa4c93343b2bf}`  
```

Agora o texto está bem mais claro e organizado. Se precisar de mais ajustes, só avisar!

> Assim, obtemos a flag `picoCTF{0n3_m0r3_t1m3_e2db86ae880862ad471aa4c93343b2bf}`  
