# Dicionário de Dados

## Objetivo do tema

Neste tema, estudaremos o dicionário de dados como um artefato de documentação e decisão arquitetural.

A ideia central é compreender que um dicionário de dados não precisa ser apenas uma lista física de tabelas, colunas e tipos. Em projetos modernos, especialmente quando comparamos bancos relacionais e bancos não relacionais, o dicionário de dados pode começar em um nível mais conceitual, descrevendo entidades, atributos, significados, regras e estruturas compostas. Depois, esse modelo pode ser desdobrado para diferentes formas de persistência: tabelas relacionais, documentos JSON, coleções, grafos ou soluções híbridas.

## 1. O que é um dicionário de dados?

Um dicionário de dados é um documento que descreve os dados importantes de um sistema.

Ele registra:

- quais entidades existem;
- quais atributos descrevem cada entidade;
- o significado de cada atributo;
- o tipo esperado de dado;
- se o atributo é obrigatório ou opcional;
- se o atributo é simples, composto ou multivalorado;
- quais regras ou restrições se aplicam;
- como os dados podem ser organizados em uma solução de banco de dados.

Em outras palavras, o dicionário de dados ajuda a responder: "Que dados o sistema precisa guardar, o que eles significam e como podem ser estruturados?"

## 2. A visão tradicional do dicionário de dados

Durante muito tempo, era comum tratar o dicionário de dados como uma descrição quase direta da estrutura física do banco relacional.

Por exemplo:

| Tabela | Campo | Tipo | Obrigatório | Descrição |
|---|---|---|---|---|
| cliente | id_cliente | inteiro | sim | Identificador do cliente |
| cliente | nome | varchar(100) | sim | Nome completo do cliente |
| cliente | cpf | char(11) | sim | CPF do cliente |
| endereco | id_endereco | inteiro | sim | Identificador do endereço |
| endereco | id_cliente | inteiro | sim | Cliente associado ao endereço |
| endereco | logradouro | varchar(150) | sim | Rua, avenida ou via |

Essa abordagem é útil, principalmente quando o projeto já decidiu que o banco será relacional e que o desenho físico das tabelas já está avançado.

Entretanto, ela tem uma limitação: mistura cedo demais a compreensão do dado com a decisão de implementação.

Antes de decidir se um dado será coluna, tabela, documento embutido ou coleção separada, precisamos entender o dado em si.

## 3. A visão moderna: primeiro entidades, depois implementação

Em projetos atuais, especialmente quando existe a possibilidade de usar bancos relacionais, bancos orientados a documentos ou arquiteturas híbridas, é interessante começar o dicionário de dados no nível das entidades.

Nesse nível, descrevemos o domínio sem assumir imediatamente uma estrutura física.

Por exemplo, podemos dizer:

**Entidade:** Cliente  
**Descrição:** Representa uma pessoa que utiliza os serviços da empresa.  

| Atributo | Tipo conceitual | Obrigatório | Característica | Descrição |
|---|---|---|---|---|
| id | identificador | sim | simples | Identificador único do cliente |
| nome | texto | sim | simples | Nome completo do cliente |
| cpf | texto | sim | simples | CPF do cliente |
| email | texto | não | simples | Endereço eletrônico de contato |
| enderecos | endereço | não | composto e multivalorado | Lista de endereços associados ao cliente |

Observe que, nesse momento, `enderecos` não precisa ser imediatamente uma tabela. Ele é entendido como um atributo da entidade `Cliente`.

Esse atributo é:

- **composto**, porque possui partes internas, como `logradouro`, `bairro`, `cidade`, `numero`, `complemento` e `cep`;
- **multivalorado**, porque um cliente pode possuir nenhum, um ou vários endereços.

## 4. Atributos simples, compostos e multivalorados

Para construir bons dicionários de dados, é importante identificar a natureza dos atributos.

### Atributo simples

É um atributo que não precisa ser dividido em partes menores para o objetivo do sistema.

Exemplos:

- `nome`
- `cpf`
- `email`
- `dataNascimento`

### Atributo composto

É um atributo formado por outros atributos.

Exemplo:

| Atributo composto | Partes |
|---|---|
| endereco | logradouro, bairro, cidade, numero, complemento, cep |

Neste caso, `endereco` não é apenas um texto solto. Ele possui uma estrutura interna.

### Atributo multivalorado

É um atributo que pode aparecer mais de uma vez para a mesma entidade.

Exemplos:

- um cliente pode ter vários telefones;
- um produto pode ter várias imagens;
- um pedido pode ter vários itens;
- um cliente pode ter vários endereços.

Quando um atributo é composto e multivalorado ao mesmo tempo, ele exige uma decisão importante de modelagem.

## 5. Exemplo: entidade Cliente com endereços

Vamos considerar a entidade `Cliente`.

### Dicionário conceitual da entidade

| Atributo | Tipo conceitual | Obrigatório | Característica | Descrição |
|---|---|---|---|---|
| id | identificador | sim | simples | Identificador único do cliente |
| nome | texto | sim | simples | Nome completo do cliente |
| cpf | texto | sim | simples | Documento de identificação fiscal |
| email | texto | não | simples | E-mail principal para contato |
| telefone | texto | não | simples | Telefone principal para contato |
| enderecos | lista de endereço | não | composto e multivalorado | Endereços vinculados ao cliente |

### Dicionário conceitual do tipo Endereco

| Atributo | Tipo conceitual | Obrigatório | Descrição |
|---|---|---|---|
| logradouro | texto | sim | Nome da rua, avenida ou via |
| bairro | texto | sim | Bairro do endereço |
| cidade | texto | sim | Cidade do endereço |
| numero | texto | sim | Número do imóvel |
| complemento | texto | não | Complemento, bloco, apartamento ou referência |
| cep | texto | sim | Código de endereçamento postal |
| tipo | texto enumerado | não | Tipo do endereço, como RESIDENCIAL ou COMERCIAL |

Repare que ainda não escolhemos se `Endereco` será uma tabela, um documento embutido ou outra estrutura. O dicionário primeiro documenta o significado e a composição do dado.

## 6. Desdobramento para banco orientado a documentos

Em um banco orientado a documentos, como MongoDB ou Firestore, pode fazer sentido armazenar os endereços dentro do próprio documento do cliente.

Exemplo de documento:

```json
{
  "_id": "cli_001",
  "nome": "Ana Ribeiro",
  "cpf": "12345678901",
  "email": "ana.ribeiro@example.com",
  "telefone": "19999990000",
  "enderecos": [
    {
      "logradouro": "Rua das Flores",
      "bairro": "Centro",
      "cidade": "Campinas",
      "numero": "120",
      "complemento": "Apto 42",
      "cep": "13000000",
      "tipo": "RESIDENCIAL"
    },
    {
      "logradouro": "Avenida Brasil",
      "bairro": "Jardim Guanabara",
      "cidade": "Campinas",
      "numero": "850",
      "complemento": "Casa",
      "cep": "13070000",
      "tipo": "COMERCIAL"
    }
  ]
}
```

Nesse caso, `enderecos` faz parte do documento `Cliente`.

Essa decisão pode ser adequada quando:

- os endereços são consultados quase sempre junto com o cliente;
- os endereços pertencem claramente ao cliente;
- não existe necessidade frequente de consultar endereços de forma independente;
- a quantidade de endereços por cliente tende a ser pequena e controlada.

O dicionário de dados, nesse cenário, pode incluir uma seção de exemplo de documento para mostrar como a entidade será persistida.

## 7. Desdobramento para banco relacional

Em uma abordagem relacional, o atributo `enderecos`, por ser multivalorado, normalmente será separado em outra tabela.

Uma possível estrutura seria:

### Tabela `CLIENTES`

| Campo | Tipo físico | Obrigatório | Observação |
|---|---|---|---|
| id_cliente | integer | sim | Chave primária |
| nome | varchar(100) | sim | Nome completo |
| cpf | char(11) | sim | Pode ter restrição de unicidade |
| email | varchar(120) | não | E-mail principal |
| telefone | varchar(20) | não | Telefone principal |

### Tabela `ENDERECOS`

| Campo | Tipo físico | Obrigatório | Observação |
|---|---|---|---|
| id_endereco | integer | sim | Chave primária |
| id_cliente | integer | sim | Chave estrangeira para `cliente` |
| logradouro | varchar(150) | sim | Rua, avenida ou via |
| bairro | varchar(100) | sim | Bairro |
| cidade | varchar(100) | sim | Cidade |
| numero | varchar(20) | sim | Número do imóvel |
| complemento | varchar(100) | não | Complemento do endereço |
| cep | char(8) | sim | Código postal |
| tipo | varchar(20) | não | Tipo do endereço, como RESIDENCIAL ou COMERCIAL |

Nesse modelo, cada endereço fica em uma linha separada da tabela `endereco`, ligado ao cliente por meio de `id_cliente`.

Essa decisão pode ser adequada quando:

- os endereços precisam ser consultados, filtrados ou atualizados de forma independente;
- existe grande volume de endereços;
- é importante aplicar integridade referencial;
- o projeto utiliza normalização relacional;
- há relatórios ou consultas SQL frequentes sobre endereços.

## 8. Comparação entre as duas abordagens

| Aspecto | Documento | Relacional |
|---|---|---|
| Representação de endereços | Embutidos no documento `Cliente` | Separados em tabela `endereco` |
| Consulta do cliente com endereços | Direta, em um único documento | Exige junção ou consulta relacionada |
| Atualização independente de endereço | Pode ser menos conveniente | Natural no modelo relacional |
| Normalização | Menor normalização, mais agregação | Maior normalização |
| Melhor quando | Os dados são acessados juntos | Os dados têm vida própria ou exigem relacionamentos fortes |

Não existe uma resposta universalmente correta. A escolha depende do padrão de uso da aplicação.

Uma pergunta central para decidir é:

> O endereço é quase sempre usado como parte do cliente ou precisa ser tratado como uma entidade independente?

## 9. O papel arquitetural do dicionário de dados

O dicionário de dados moderno não serve apenas para listar campos. Ele ajuda a tomar decisões.

Ele permite discutir:

- quais dados pertencem naturalmente a uma entidade;
- quais atributos são simples, compostos ou multivalorados;
- quais dados devem ser embutidos;
- quais dados devem ser referenciados;
- quais dados devem ser normalizados;
- quais dados podem ser duplicados por desempenho;
- quais estruturas fazem sentido para o padrão de consulta esperado.

Em bancos não relacionais, essa discussão é ainda mais importante, porque a modelagem não começa pela pergunta "quais tabelas existem?", mas por perguntas como:

- como a aplicação consulta esses dados?
- quais dados precisam aparecer juntos?
- qual é o tamanho esperado dos documentos?
- quais campos mudam com frequência?
- quais estruturas podem crescer indefinidamente?
- quais dados precisam de consistência forte?

## 10. Modelo sugerido de dicionário de dados para a disciplina

Para esta disciplina, podemos organizar o dicionário de dados em três níveis.

### Nível 1: Entidades e atributos conceituais

Descreve o domínio sem assumir uma tecnologia específica.

| Entidade | Descrição |
|---|---|
| Cliente | Pessoa que utiliza os serviços da empresa |

| Atributo | Tipo conceitual | Obrigatório | Característica | Descrição |
|---|---|---|---|---|
| id | identificador | sim | simples | Identificador único |
| nome | texto | sim | simples | Nome completo |
| enderecos | lista de endereço | não | composto e multivalorado | Endereços vinculados ao cliente |

### Nível 2: Estruturas internas

Descreve atributos compostos ou estruturas reutilizáveis.

| Estrutura | Atributo | Tipo conceitual | Obrigatório | Descrição |
|---|---|---|---|---|
| Endereco | logradouro | texto | sim | Rua, avenida ou via |
| Endereco | bairro | texto | sim | Bairro |
| Endereco | cidade | texto | sim | Cidade |
| Endereco | numero | texto | sim | Número |
| Endereco | complemento | texto | não | Informação adicional |
| Endereco | cep | texto | sim | Código postal |
| Endereco | tipo | texto enumerado | não | Tipo do endereço, como RESIDENCIAL ou COMERCIAL |

### Nível 3: Desdobramento físico ou tecnológico

Mostra como a entidade será implementada em uma tecnologia específica.

Esse nível pode conter:

NoSQL orientado a documentos:
- exemplo de documento JSON;
- definição de coleção;

Se banco relacional: 
- definição de tabelas relacionais;
- indicação de chaves primárias e estrangeiras;

Em ambas as tecnologias:
- índices previstos;
- exemplos de consultas importantes;
- justificativa para embutir ou referenciar dados.


## Atividades

Aqui no repositório, no diretório `exemplos`, temos um template de dicionário de dados que pode servir de base para criar dicionários e modelos iniciais com desdobramentos.
