# Consultas no MongoDB

## Preparação

Para trabalhar com consultas básicas no MongoDB, acesse sua conta no MongoDB Atlas, crie uma organização chamada `Exemplo`, um projeto chamado `Consultas` e um cluster também chamado `Consultas`, do tipo `M0`.

Lembre-se de que será solicitada a criação de um usuário para acessar o cluster e também a liberação do IP a partir da PUC, ou `0.0.0.0/0`, como o professor já explicou.

Carregue todos os bancos de exemplo, chamados de `samples`, que o MongoDB Atlas disponibiliza no seu cluster.

Para fazer essa tarefa, após criar seu cluster, acesse-o pelo menu **Databases -> Cluster -> botão "..." -> Load sample data**.

Aguarde alguns minutos, pois o MongoDB Atlas criará diversos bancos de dados de exemplo, já preenchidos com documentos para você praticar consultas. Carregar esses bancos costuma demorar de 5 a 10 minutos. No entanto, será necessário fazer isso apenas uma vez.

Após todos os bancos de exemplo serem provisionados no cluster, abra o MongoDB Compass e conecte-se ao cluster que você acabou de criar.

Todas as consultas deste laboratório devem ser feitas tanto pela interface gráfica do Compass quanto pelo terminal no Mongo Shell. Acesse o Mongo Shell pelo próprio Compass.

Expanda o banco de dados `sample_mflix` e note que existem diversas coleções.

## Banco `sample_mflix`

### Coleção `movies`

1. Faça uma consulta que retorne todos os filmes do ano de 1999.

2. Faça uma consulta que retorne todos os filmes a partir de 2010, incluindo 2010.

3. Faça uma consulta que retorne todos os filmes a partir de 2010, incluindo 2010, e que o tempo de duração em minutos (`runtime`) seja menor ou igual a 150.

4. Altere a consulta anterior para considerar somente filmes com rating do IMDb igual a 5.5.

5. Faça uma consulta que busque filmes disponibilizados no idioma francês, podendo também ter outros idiomas.

6. Faça uma consulta que busque somente os filmes que foram disponibilizados apenas no idioma francês.

7. Consulte os filmes que contenham exatamente 3 idiomas.

8. João pensou que, com esta consulta, poderia retornar os filmes que possuem mais de 4 writers:

```javascript
{ "writers": { $size: { $gt: 4 } } }
```

Essa consulta consegue realizar esse trabalho? Sim ou não? Faça o teste.

9. Faça uma consulta que retorne todos os filmes que tiveram rating do IMDb 5.5 e venceram 4 prêmios.

10. Digite essa busca:

```javascript
{
  "title": { $exists: true },
  $expr: { $gt: [ { $strLenCP: "$title" }, 100 ] }
}
```

O que ela retorna?

O que faz a primeira linha?

O que faz a segunda linha e por que a primeira linha é necessária?

## Banco `sample_restaurants`

11. Busque todos os restaurantes que estão no distrito do Brooklyn:

```javascript
{ "borough": "Brooklyn" }
```

12. Altere a consulta anterior para mostrar todos os restaurantes que estão no Brooklyn e que são especializados em comida judaica/kosher.

13. Altere a consulta anterior para que retorne todos os restaurantes, independentemente de distrito, que sejam de cozinha judaica ou irlandesa.

14. Retorne todos os restaurantes de cozinha americana que contenham um score (`grades.score`) 60.

15. Modifique a consulta anterior para retornar todos os restaurantes que tenham algum score acima de 130, independentemente do tipo de cozinha.

16. Estude o operador `$regex` do MongoDB para procurar todos os restaurantes que tenham a substring `burger` em qualquer posição do campo `name`.

17. Modifique a questão anterior para buscar todos os restaurantes que comecem com a string `Angel`, considerando que o `A` maiúsculo importa.

18. Busque todos os restaurantes que estão no zipcode `11210`.

19. Busque todos os restaurantes cujo `borough` seja `Manhattan` e `cuisine` seja `Steak`.

## Banco `sample_analytics`

20. Na coleção `customers`, faça uma consulta que busque o documento do usuário que possui o email:

```text
cooperalexis@hotmail.com
```

21. Ainda na coleção `customers`, busque todos os clientes que usam email do Gmail.

22. Supondo que existisse um campo `createdAt` no documento que representasse quando ele foi criado, com data e hora, para buscar os documentos criados a partir de 01/02/2024, faríamos:

```javascript
{ createdAt: { $gte: ISODate("2024-02-01") } }
```

Considerando esse aprendizado, faça uma busca de todos os clientes que nasceram após 01/01/1997.

23. Na coleção `accounts`, procure todas as contas que tenham somente dois produtos, ou seja, dois elementos no array `products`.

24. Na coleção `accounts`, procure todas as contas que tenham entre os produtos somente o produto `InvestmentStock`.

## Banco `sample_airbnb`

25. Procure todas as acomodações que possuam preço entre 1000 e 2000.

26. Procure todas as acomodações que possuam 14 camas ou custem por noite mais de 20.000.

27. Consulta livre 1 na coleção de documentos do Airbnb, com algum conceito novo de sua livre escolha.

28. Consulta livre 2 na coleção de documentos do Airbnb, com algum conceito novo de sua livre escolha.

29. Consulta livre 3 na coleção de documentos do Airbnb, com algum conceito novo de sua livre escolha.

30. Faça uma consulta que retorne acomodações que possuam a comodidade `Wifi` e que tenham nota de avaliação (`review_scores.review_scores_rating`) maior ou igual a 90.
