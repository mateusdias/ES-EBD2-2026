# Abordagens Arquiteturais para Modelagem de Documentos

## Objetivo do tema

Neste tema, estudaremos como tomar decisões arquiteturais ao modelar dados em bancos orientados a documentos.

A ideia central é compreender que modelar documentos não é apenas transformar tabelas relacionais em arquivos JSON. Em bancos como MongoDB, Firestore, DynamoDB e outros bancos orientados a documentos ou chave-documento, a estrutura dos dados deve considerar como a aplicação consulta, atualiza, agrega, filtra e evolui suas informações.

Em outras palavras, a pergunta principal deixa de ser apenas:

> Quais entidades existem?

E passa a incluir perguntas como:

- quais dados são lidos juntos?
- quais dados mudam juntos?
- quais dados têm vida própria?
- quais listas podem crescer muito?
- quais relacionamentos precisam ser navegados com frequência?
- onde vale a pena duplicar informação?
- onde a duplicação pode causar inconsistência?

## 1. Modelagem orientada a documentos

Um banco orientado a documentos armazena dados em estruturas semelhantes a JSON.

Um documento pode conter:

- atributos simples;
- objetos aninhados;
- listas de valores;
- listas de objetos;
- referências para outros documentos;
- campos duplicados para facilitar consultas.

Exemplo simples de documento:

```json
{
  "_id": "cli_001",
  "nome": "Ana Ribeiro",
  "email": "ana.ribeiro@example.com",
  "enderecos": [
    {
      "tipo": "RESIDENCIAL",
      "logradouro": "Rua das Flores",
      "numero": "120",
      "cidade": "Campinas",
      "uf": "SP"
    }
  ]
}
```

Esse exemplo mostra uma característica importante: o endereço não está em uma tabela separada. Ele está aninhado dentro do documento do cliente.

Isso pode ser bom ou ruim. A qualidade da decisão depende do contexto.

## 2. Agregados: a unidade de modelagem

Em modelagem de documentos, uma técnica importante é pensar em **agregados**.

Um agregado é um conjunto de dados que faz sentido ser tratado como uma unidade.

Exemplos:

- um `Pedido` com seus `itens`;
- um `Cliente` com seus `enderecos`;
- um `Produto` com suas `imagens`;
- uma `Postagem` com seus `comentarios`;
- uma `Turma` com seus `alunos`.

O agregado não é apenas uma estrutura de armazenamento. Ele representa uma decisão arquitetural:

> Estes dados devem ser carregados, salvos e compreendidos juntos?

Se a resposta for sim, provavelmente existe uma boa justificativa para aninhar documentos.

Se a resposta for não, talvez seja melhor separar em coleções diferentes e usar referências.

## 3. Estilo 1: Documento agregado

No estilo de documento agregado, os dados relacionados ficam dentro do mesmo documento.

Exemplo: pedido com itens.

```json
{
  "_id": "ped_001",
  "clienteId": "cli_001",
  "data": "2026-08-17",
  "status": "PAGO",
  "itens": [
    {
      "produtoId": "prod_101",
      "nomeProduto": "Teclado Mecânico",
      "quantidade": 1,
      "precoUnitario": 250.00
    },
    {
      "produtoId": "prod_205",
      "nomeProduto": "Mouse sem fio",
      "quantidade": 2,
      "precoUnitario": 80.00
    }
  ],
  "valorTotal": 410.00
}
```

Neste caso, os itens pertencem ao pedido. Um item de pedido não costuma existir sozinho fora do pedido.

Essa abordagem é adequada quando:

- os dados filhos são consultados quase sempre junto com o documento pai;
- os dados filhos não precisam ser acessados de forma independente;
- o número de itens é limitado ou previsível;
- a atualização do conjunto inteiro faz sentido;
- existe uma relação forte de composição.

Vantagens:

- leitura simples;
- menos consultas;
- menos junções na aplicação;
- documento mais próximo da tela ou da resposta da API;
- boa performance para consultas por agregado.

Riscos:

- documentos muito grandes;
- listas internas crescendo indefinidamente;
- dificuldade para consultar itens isoladamente;
- dificuldade para atualizar partes muito específicas em grande volume;
- maior risco de conflito quando muitos usuários atualizam o mesmo documento.

## 4. Estilo 2: Documentos separados com referência

No estilo com referência, cada entidade principal fica em sua própria coleção. O relacionamento é representado por identificadores.

Exemplo: cliente e pedidos em coleções separadas.

Documento em `clientes`:

```json
{
  "_id": "cli_001",
  "nome": "Ana Ribeiro",
  "email": "ana.ribeiro@example.com"
}
```

Documento em `pedidos`:

```json
{
  "_id": "ped_001",
  "clienteId": "cli_001",
  "data": "2026-08-17",
  "status": "PAGO",
  "valorTotal": 410.00
}
```

Nesse modelo, o pedido aponta para o cliente por meio de `clienteId`.

Essa abordagem é adequada quando:

- as entidades têm vida própria;
- os dados são consultados separadamente;
- a quantidade de registros relacionados pode crescer muito;
- é necessário filtrar, ordenar ou paginar os filhos;
- diferentes partes do sistema atualizam os documentos separadamente;
- o relacionamento lembra uma associação, e não uma composição.

Vantagens:

- documentos menores;
- melhor controle sobre crescimento;
- consultas independentes mais simples;
- menor risco de atualizar um documento gigante;
- aproximação mais natural de modelos relacionais.

Riscos:

- mais consultas para montar uma visão completa;
- necessidade de resolver referências na aplicação;
- possibilidade de inconsistência se documentos relacionados forem atualizados separadamente;
- maior cuidado com índices.

## 5. Estilo 3: Modelo híbrido

Em muitos sistemas reais, a melhor solução não é totalmente agregada nem totalmente separada.

É comum combinar:

- documento principal;
- dados aninhados;
- referências;
- pequenos dados duplicados para facilitar leitura.

Exemplo: pedido com referência ao cliente, mas com alguns dados do cliente copiados.

```json
{
  "_id": "ped_001",
  "cliente": {
    "id": "cli_001",
    "nome": "Ana Ribeiro",
    "email": "ana.ribeiro@example.com"
  },
  "data": "2026-08-17",
  "status": "PAGO",
  "itens": [
    {
      "produtoId": "prod_101",
      "nomeProduto": "Teclado Mecânico",
      "quantidade": 1,
      "precoUnitario": 250.00
    }
  ],
  "valorTotal": 250.00
}
```

Neste exemplo, o pedido não guarda todos os dados do cliente, mas guarda um resumo útil.

Essa duplicação pode fazer sentido porque:

- o pedido precisa exibir rapidamente o nome do cliente;
- o histórico do pedido deve preservar o nome usado no momento da compra;
- a aplicação evita buscar o cliente em toda listagem de pedidos.

O ponto importante é que duplicação em NoSQL não é necessariamente erro. Ela pode ser uma decisão arquitetural consciente.

## 6. Quando aninhar documentos?

Aninhar significa colocar um objeto ou lista de objetos dentro de outro documento.

Exemplo:

```json
{
  "_id": "cli_001",
  "nome": "Ana Ribeiro",
  "telefones": [
    {
      "tipo": "CELULAR",
      "numero": "19999990000"
    },
    {
      "tipo": "COMERCIAL",
      "numero": "1933334444"
    }
  ]
}
```

Aninhar costuma ser uma boa escolha quando:

- existe relação de pertencimento;
- o dado filho não faz sentido sozinho;
- o dado filho é pequeno;
- a lista é pequena ou tem limite claro;
- pai e filho são lidos juntos;
- pai e filho são atualizados juntos;
- não há necessidade frequente de buscar o filho globalmente.

Exemplos comuns de aninhamento:

| Documento principal | Dado aninhado | Justificativa |
|---|---|---|
| Cliente | telefones | Telefones pertencem ao cliente e costumam ser poucos |
| Cliente | enderecos | Endereços costumam ser consultados junto com o cliente |
| Pedido | itens | Itens pertencem ao pedido |
| Produto | imagens | Imagens são parte da apresentação do produto |
| Postagem | tags | Tags simples podem ser lidas junto com a postagem |

## 7. Quando não aninhar documentos?

Nem todo relacionamento deve virar objeto interno.

Não aninhar costuma ser melhor quando:

- a lista pode crescer sem limite;
- o dado filho é consultado independentemente;
- o dado filho é atualizado com muita frequência;
- muitos usuários podem atualizar partes diferentes ao mesmo tempo;
- o mesmo dado filho pertence a vários documentos;
- o dado filho precisa de permissões, ciclo de vida ou auditoria próprios;
- as consultas precisam filtrar, ordenar ou paginar os filhos de forma intensa.

Exemplo de risco: colocar todos os comentários dentro de uma postagem muito popular.

```json
{
  "_id": "post_001",
  "titulo": "Introdução a NoSQL",
  "comentarios": [
    {
      "usuarioId": "usr_001",
      "texto": "Muito bom!",
      "data": "2026-08-17T10:00:00"
    }
  ]
}
```

Esse modelo pode funcionar para poucos comentários. Porém, se a postagem receber milhares de comentários, o documento pode ficar grande demais, difícil de atualizar e ruim para paginação.

Uma alternativa seria separar os comentários:

```json
{
  "_id": "com_001",
  "postId": "post_001",
  "usuarioId": "usr_001",
  "texto": "Muito bom!",
  "data": "2026-08-17T10:00:00"
}
```

Assim, a aplicação pode buscar comentários por `postId`, paginar resultados e indexar campos importantes.

## 8. Cardinalidade e crescimento

A cardinalidade ajuda a decidir entre aninhar e referenciar.

Cardinalidade indica quantos registros de um tipo podem se relacionar com outro.

| Relação | Exemplo | Tendência de modelagem |
|---|---|---|
| Um para um | Cliente e perfil | Pode aninhar se forem lidos juntos |
| Um para poucos | Cliente e telefones | Geralmente pode aninhar |
| Um para muitos | Cliente e pedidos | Geralmente separar |
| Um para muitos sem limite | Postagem e comentários | Geralmente separar |
| Muitos para muitos | Alunos e disciplinas | Geralmente separar ou criar coleção intermediária |

A pergunta mais importante não é apenas "quantos existem hoje?", mas:

> Quantos podem existir no uso real do sistema?

Uma lista pequena no início pode virar um problema se não houver limite arquitetural.

## 9. Direção da consulta

Em bancos orientados a documentos, modelar bem exige entender o caminho das consultas.

Compare duas perguntas:

1. "Dado um cliente, quero ver seus endereços."
2. "Dado um CEP ou cidade, quero encontrar todos os clientes com endereço nessa região."

No primeiro caso, aninhar endereços no cliente pode ser natural.

No segundo caso, talvez seja necessário indexar campos internos, duplicar dados ou até separar endereços em outra coleção, dependendo da tecnologia e do volume.

Outro exemplo:

1. "Dado um pedido, quero ver seus itens."
2. "Dado um produto, quero listar todos os pedidos em que ele apareceu."

No primeiro caso, itens aninhados no pedido funcionam bem.

No segundo caso, a consulta global por item pode ficar mais complexa.

Por isso, a modelagem deve começar pelos principais casos de uso.

## 10. Frequência de leitura e escrita

Uma estrutura boa para leitura pode ser ruim para escrita.

Uma estrutura boa para escrita pode exigir mais trabalho na leitura.

Exemplo:

- uma tela de listagem de pedidos precisa mostrar `numeroPedido`, `nomeCliente`, `status` e `valorTotal`;
- se o nome do cliente estiver apenas na coleção `clientes`, a aplicação pode precisar buscar vários clientes para montar a lista;
- duplicar `nomeCliente` no documento do pedido pode melhorar a leitura;
- mas se o cliente mudar de nome, surge a pergunta: os pedidos antigos devem mudar também?

Esse tipo de decisão depende da regra de negócio.

Se o pedido deve preservar o histórico, duplicar o nome é correto.

Se o pedido deve sempre mostrar o nome atualizado, talvez seja melhor referenciar.

## 11. Duplicação controlada

Em bancos relacionais, aprendemos que duplicação geralmente é um problema de normalização.

Em bancos orientados a documentos, duplicação pode ser uma técnica de desempenho e simplificação de leitura.

Exemplo de documento de produto:

```json
{
  "_id": "prod_101",
  "nome": "Teclado Mecânico",
  "categoria": {
    "id": "cat_010",
    "nome": "Periféricos"
  },
  "preco": 250.00
}
```

O produto guarda o `id` e o `nome` da categoria.

Isso evita uma consulta adicional quando a tela de produtos precisa exibir a categoria.

Porém, essa decisão exige uma regra:

- se o nome da categoria mudar, os produtos antigos serão atualizados?
- ou o nome copiado representa uma fotografia histórica?

Duplicação controlada precisa de responsabilidade clara.

Documente:

- qual campo foi duplicado;
- de onde ele veio;
- por que foi duplicado;
- quando ele deve ser atualizado;
- quem é a fonte oficial do dado.

## 12. Fonte oficial do dado

Quando existe duplicação, é preciso saber qual documento contém a verdade principal.

Exemplo:

| Dado | Fonte oficial | Cópias permitidas | Regra |
|---|---|---|---|
| Nome do cliente | `clientes.nome` | `pedidos.cliente.nome` | No pedido, representa o nome no momento da compra |
| Nome da categoria | `categorias.nome` | `produtos.categoria.nome` | Atualizar produtos se a categoria mudar |
| Preço do produto | `produtos.precoAtual` | `pedidos.itens.precoUnitario` | No pedido, preservar preço histórico |

Essa tabela evita uma confusão comum: achar que todo valor repetido precisa ser sincronizado.

Algumas cópias são históricas. Outras são cache. Outras são resumo.

Cada caso precisa de uma regra.

## 13. Técnicas de modelagem

### Começar pelas consultas

Liste as principais perguntas que o sistema precisa responder.

Exemplos:

- buscar cliente por CPF;
- listar pedidos de um cliente;
- exibir detalhes de um pedido;
- listar produtos por categoria;
- buscar comentários de uma postagem com paginação;
- gerar relatório de vendas por período.

Depois, modele os documentos para responder bem às consultas mais importantes.

### Desenhar documentos de exemplo

Antes de criar coleções definitivas, escreva exemplos reais em JSON.

Isso ajuda a perceber:

- campos faltando;
- listas grandes demais;
- objetos aninhados confusos;
- duplicações necessárias;
- consultas difíceis;
- nomes inconsistentes.

### Definir limites de crescimento

Sempre que houver uma lista aninhada, pergunte:

- qual é o tamanho médio esperado?
- qual é o tamanho máximo aceitável?
- existe limite de negócio?
- a lista precisa ser paginada?
- a lista será atualizada com frequência?

Se não existe limite claro, a lista talvez não deva ficar aninhada.

### Separar leitura principal de análise

Nem toda consulta analítica precisa definir o modelo transacional principal.

Um sistema pode ter:

- documentos otimizados para a aplicação;
- índices para consultas frequentes;
- coleções de resumo;
- pipelines de agregação;
- exportações para relatórios.

Forçar o documento principal a responder todas as perguntas possíveis pode deixar a modelagem pesada.

### Registrar decisões arquiteturais

Cada decisão importante deve ter justificativa.

Exemplo:

| Decisão | Justificativa |
|---|---|
| Aninhar `itens` em `pedidos` | Itens pertencem ao pedido e são sempre exibidos com ele |
| Separar `comentarios` de `postagens` | Comentários podem crescer muito e precisam de paginação |
| Duplicar `cliente.nome` em `pedidos` | Listagem de pedidos precisa mostrar o nome sem consulta adicional |
| Preservar `precoUnitario` no item do pedido | O pedido deve manter o preço histórico da compra |

## 14. Padrões comuns de modelagem

### Um para poucos

Quando uma entidade possui poucos elementos filhos, aninhar costuma ser adequado.

Exemplo:

```json
{
  "_id": "cli_001",
  "nome": "Ana Ribeiro",
  "telefones": [
    {
      "tipo": "CELULAR",
      "numero": "19999990000"
    }
  ]
}
```

### Um para muitos

Quando uma entidade possui muitos elementos filhos, separar costuma ser mais seguro.

Exemplo:

```json
{
  "_id": "ped_001",
  "clienteId": "cli_001",
  "data": "2026-08-17",
  "valorTotal": 410.00
}
```

Os pedidos ficam separados do cliente, mesmo pertencendo ao histórico dele.

### Muitos para muitos

Relacionamentos muitos para muitos geralmente exigem documentos próprios ou arrays de referências bem controlados.

Exemplo: alunos e disciplinas.

Documento em `matriculas`:

```json
{
  "_id": "mat_001",
  "alunoId": "alu_001",
  "disciplinaId": "dis_010",
  "periodo": "2026-2",
  "status": "ATIVA"
}
```

Esse modelo evita colocar listas enormes de disciplinas dentro de alunos ou listas enormes de alunos dentro de disciplinas.

### Snapshot histórico

Um snapshot guarda uma cópia do dado no momento de um evento.

Exemplo:

```json
{
  "_id": "ped_001",
  "cliente": {
    "id": "cli_001",
    "nome": "Ana Ribeiro"
  },
  "itens": [
    {
      "produtoId": "prod_101",
      "nomeProduto": "Teclado Mecânico",
      "precoUnitario": 250.00
    }
  ]
}
```

Mesmo que o produto mude de nome ou preço, o pedido preserva o que aconteceu no momento da compra.

### Resumo para listagem

Um resumo guarda apenas os campos necessários para uma tela ou consulta frequente.

Exemplo:

```json
{
  "_id": "post_001",
  "titulo": "Introdução a NoSQL",
  "autor": {
    "id": "usr_001",
    "nome": "Lucas Almeida"
  },
  "quantidadeComentarios": 18,
  "ultimaInteracaoEm": "2026-08-17T14:30:00"
}
```

Esse tipo de modelagem evita recalcular informações repetidamente.

## 15. Erros comuns

### Copiar o modelo relacional diretamente

Criar uma coleção para cada tabela pode desperdiçar as vantagens do modelo de documentos.

Exemplo ruim:

- `clientes`;
- `telefones`;
- `enderecos`;
- `emails`;
- `itensPedido`.

Às vezes isso é necessário, mas não deve ser automático.

### Aninhar tudo

O erro oposto também acontece.

Colocar tudo dentro de um único documento pode gerar:

- documentos enormes;
- concorrência difícil;
- consultas limitadas;
- dificuldade de manutenção;
- problemas de crescimento.

### Ignorar o padrão de acesso

Modelar sem saber como os dados serão consultados leva a documentos bonitos, mas pouco úteis.

A modelagem deve ser testada contra casos reais:

- telas do sistema;
- APIs;
- relatórios;
- filtros;
- ordenações;
- regras de atualização.

### Duplicar sem regra

Duplicação sem documentação cria inconsistência.

Sempre que duplicar, defina se o dado copiado é:

- histórico;
- resumo;
- cache;
- conveniência de leitura;
- fonte oficial.

## 16. Checklist de decisão

Antes de decidir se um dado será aninhado, referenciado ou duplicado, responda:

| Pergunta | Se a resposta for sim |
|---|---|
| O dado filho pertence fortemente ao pai? | Considere aninhar |
| O dado filho é pequeno e limitado? | Considere aninhar |
| Pai e filho são consultados juntos? | Considere aninhar |
| O dado filho tem vida própria? | Considere separar |
| A lista pode crescer muito? | Considere separar |
| O dado filho precisa ser paginado? | Considere separar |
| O dado filho é atualizado com muita frequência? | Considere separar |
| A tela precisa evitar consultas adicionais? | Considere duplicar campos de resumo |
| O valor precisa preservar histórico? | Considere snapshot |
| Existe risco de inconsistência? | Documente fonte oficial e regra de atualização |

## 17. Exemplo comparativo completo

Vamos considerar um sistema de comércio eletrônico.

### Alternativa A: cliente com pedidos aninhados

```json
{
  "_id": "cli_001",
  "nome": "Ana Ribeiro",
  "pedidos": [
    {
      "_id": "ped_001",
      "data": "2026-08-17",
      "valorTotal": 410.00
    }
  ]
}
```

Essa alternativa pode parecer simples, mas tende a ser inadequada. Um cliente pode fazer muitos pedidos ao longo do tempo. Além disso, pedidos precisam ser filtrados por data, status, pagamento, entrega e outros critérios.

### Alternativa B: clientes e pedidos separados

```json
{
  "_id": "cli_001",
  "nome": "Ana Ribeiro",
  "email": "ana.ribeiro@example.com"
}
```

```json
{
  "_id": "ped_001",
  "clienteId": "cli_001",
  "clienteNome": "Ana Ribeiro",
  "data": "2026-08-17",
  "status": "PAGO",
  "valorTotal": 410.00
}
```

Essa alternativa costuma ser melhor. O cliente tem vida própria, o pedido também, e a relação entre eles é mantida por referência.

O campo `clienteNome` pode ser duplicado para facilitar listagens.

### Alternativa C: pedido com itens aninhados

```json
{
  "_id": "ped_001",
  "clienteId": "cli_001",
  "data": "2026-08-17",
  "status": "PAGO",
  "itens": [
    {
      "produtoId": "prod_101",
      "nomeProduto": "Teclado Mecânico",
      "quantidade": 1,
      "precoUnitario": 250.00
    }
  ],
  "valorTotal": 250.00
}
```

Essa alternativa costuma fazer sentido. Os itens são parte do pedido e precisam preservar uma fotografia do momento da compra.

## 18. Relação com o dicionário de dados

As decisões de modelagem de documentos devem aparecer no dicionário de dados.

Não basta listar campos. É importante registrar:

- se o atributo será simples, composto ou multivalorado;
- se o atributo será aninhado ou referenciado;
- se haverá duplicação;
- qual é a fonte oficial do dado;
- quais índices são necessários;
- quais consultas justificam a estrutura;
- quais limites de crescimento foram assumidos.

Exemplo:

| Entidade | Atributo | Estratégia | Justificativa |
|---|---|---|---|
| Pedido | itens | aninhado | Itens pertencem ao pedido e são lidos junto com ele |
| Pedido | clienteId | referência | Cliente tem vida própria e pode ter muitos pedidos |
| Pedido | clienteNome | duplicado | Facilita listagem de pedidos sem buscar cliente |
| Pedido | itens.precoUnitario | snapshot | Preserva preço no momento da compra |

## 19. Síntese

Modelar documentos é escolher fronteiras.

A principal decisão é definir o que fica junto e o que fica separado.

Em geral:

- aninhe quando houver pertencimento, leitura conjunta e crescimento limitado;
- referencie quando houver independência, crescimento elevado ou consultas próprias;
- duplique quando a leitura justificar, mas documente a fonte oficial;
- use snapshots quando o histórico for mais importante que o valor atualizado;
- modele a partir dos casos de uso, não apenas das entidades.

Uma boa modelagem orientada a documentos não é a mais normalizada nem a mais aninhada. É aquela que representa bem o domínio, responde bem às consultas importantes e deixa claras as decisões arquiteturais tomadas.

## Atividades

1. Escolha uma entidade do projeto em desenvolvimento na disciplina.
2. Identifique quais atributos são simples, compostos e multivalorados.
3. Liste três consultas importantes que a aplicação precisará responder.
4. Decida quais dados serão aninhados, referenciados ou duplicados.
5. Justifique cada decisão usando o checklist deste tema.
6. Registre essas decisões no dicionário de dados do projeto.
