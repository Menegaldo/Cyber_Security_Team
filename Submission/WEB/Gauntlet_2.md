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

Ao acessar o site com a 2 questão, percebi que ela seria quase igual a web gauntlet 2, por ter o mesmo esquema de uma página de Login e Senha.
![image](https://github.com/user-attachments/assets/286fe1ca-4336-46bd-80ef-d882e5392d3d)

e depois acessei o filter.php, que no primeiro exercício mostrava quais eram os filtros para não usar no comando da Query. No caso desse exercício é:
![image](https://github.com/user-attachments/assets/d59a122f-447c-4c72-a41b-3b0f023db98f)

Minhas primeiras tentativas eu procurei o ChatGpt e ele me deu algumas alternativas como:
LOGIN: admin' AND 1=1 AND (SELECT name FROM sqlite_master WHERE type='table') --  
SENHA: qualquer coisa


Porém essa tentativa já não foi favorável para mim, pois o site tem um filtro de tamanho de caracter também:
![image](https://github.com/user-attachments/assets/ae16e14f-c01e-477c-9b32-bec59c104f81)



Depois disso, pedi pro gpt me passar todos os comandos do SQLite:
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
=
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

Com todas essas opções comecei a tentar esses comandos, de alguma maneira para que o valor desse positivo e que nenhum filtro barrasse, mas nenhuma ideia que funcionasse. Com isso pedi ajuda ao gpt e ele falou pra eu tentar comparar 2 números diferentes com o IS NOT pra que o valor seja TRUE para que a lógica no SQLite me deixe acessar o site.

Minha primeira tentativa foi
LOGIN: 'qualquer coisa'
Senha: 1' IS NOT '2
![image](https://github.com/user-attachments/assets/3e517e79-1815-433c-b75d-a382d167db5b)
![image](https://github.com/user-attachments/assets/fdbf8787-3af3-4c71-ac97-5ee47694a88e)


E vi que também não adiantaria eu tentar entrar com outro usuário, com isso voltei ao WEB GAUNTLET 1 para ver como era o login de admin para burlar o filtro.
E eu tinha que jogar no campo de LOGIN: ad'||'min
e aí eu tentei novamente no campo SENHA: 1' IS NOT '2
E com isso eu consegui o login no site.
![image](https://github.com/user-attachments/assets/1f2b11fe-7eae-40e4-b8dd-779e79cd8a86)
![image](https://github.com/user-attachments/assets/5d4ccf40-2239-47dc-b20e-9391161df011)

Depois acessei o filter.php e ele me apresentou o seguinte:


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

> Assim, obtemos a flag `picoCTF{0n3_m0r3_t1m3_e2db86ae880862ad471aa4c93343b2bf}`  
