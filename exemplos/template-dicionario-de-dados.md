# Dicionário de Dados: Projeto XY
> **DICA:** No título devemos nomear corretamente o dicionário, de maneira que seja bem específico e aplicado. Exemplo: Dicionário de Dados: Sistema de locação de veículos da Localiza/SA. Neste título entendemos que o dicionário pertence a um PROJETO da empresa Localiza/SA e não de um produto de software que será oferecido.

## 1. Histórico de versões (ou revisões)

> Colocar em uma tabela, em ordem decrescente (de preferência) as atualizações e histórico do dicionário. Sempre faça esse quadro, afinal isso pode ser parte dos artefatos entregáveis do projeto e esse relato histórico serve como evidencia comprobatória de trabalho de requisitos.

> Observe a tabela abaixo e depois utilize a vazia para preencher com seu histórico. 

| Data | Autor | Versão | Comentários |
| :---: | :---: | ---: | :--- |
|07/08/2026| Mateus Dias | 1.0.0 | Publicação da versão 1.0.0 |
|06/08/2026| João Silva | 1.0.0-alfa6 | Ajustes após a revisão por pares |
|05/08/2026| João Silva | 1.0.0-alfa5 | Ajustes finais pré revisão por pares |
|04/08/2026| Mateus Dias | 1.0.0-alfa4 | Desdobramento das entidades em exemplos relacionais e não relacionais |
|03/08/2026| Mateus Dias | 1.0.0-alfa3 | Dicionarização das entidades Veículo, Condutor e Locação |
|02/08/2026| Mateus Dias | 1.0.0-alfa2 | Dicionarização da entidade Cliente |
|01/08/2026| Mateus Dias | 1.0.0-alfa1 | Versão inicial - Título e estrutura do documento, citação das entidades e suas descrições |

> Remova a tabela de exemplo acima e preencha esta em branco: 

| Data | Autor | Versão | Comentários |
| :---: | :---: | ---: | :--- |
| dd/mm/aaaa | Nome do Autor | 1.0.0-alfa1 | ...

## 2. Entidades e atributos conceituais

A seguir serão descritas as entidades que o projeto deverá contemplar, seus atributos, tipos conceituais, características e comentários sobre cada atributo contendo exemplos aceitos e não aceitos. Além de observações pertinentes a cada uma das entidades.

> Nesta seção, descreva os dados em nível conceitual. Ou seja: ainda não é necessário decidir se um atributo será uma coluna, uma tabela, um campo embutido em documento JSON ou uma coleção separada. Primeiro descreva o dado, seu significado e suas regras.

> Sugestão: Descrever as entidades mais importantes para o sistema/negócio primeiro. Por exemplo: Se você está trabalhando em um projeto de sistema de comércio eletrônico, talvez as entidades mais importantes sejam: Cliente, Produto, Carrinho etc.

### 2.1. <Nome da Entidade 1> (sempre no singular) 

Coloque aqui a descrição da entidade e porque ela deve ser representada no banco de dados e qual sua importância para o negócio. 

> Exemplo de descrição: Cliente é caracterizado como aquele que contrata os serviços de locação de veículos da Localiza. Pode ser uma pessoa jurídica com CNPJ ou pessoa física com CPF. Estrangeiros que alugam veículos também são tratados como clientes pessoa física, onde o documento utilizado seja o passaporte. 

#### Atributos de Entidade 1

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
|:---|:---:|:---:|:---:|:---|:---|:---|
|nome| Texto alfanumérico | Sim | Simples | João da Silva, 123 Log&Cia, @SuperNet | " ", ou "" ou qualquer nome com menos de 10 caracteres | Observar que o nome pode ser tanto de pessoa física quanto a razão social jurídica. Ao inserir ou alterar o nome, sempre garantir que todos os caracteres sejam transformados em maiúsculo. |
|documento| Documento de identificação | Sim | Composto (C) | Aceitos somente quando o tipo do documento for CPF, CNPJ ou PASSAPORTE e, caso CPF, deve ser validado de acordo com as regras de 11 números; CNPJ deve possuir 14 caracteres; já o passaporte deve possuir pelo menos 5 caracteres alfanuméricos. | Não serão aceitos, em hipótese alguma, valores brancos ou nulos no campo valor do documento. | Consultar a seção de tipos especiais para saber como é o tipo Documento de identificação e suas regras de validação. |


### 2.2. <Nome da Entidade 2>

Descrição da entidade 2... 

### 2.3 <Nome da Entidade 3>

Descrição da entidade 3... 

### 2.4. <Nome da Entidade 4>

Descrição da entidade 4... 

## 3. Tipos especiais, compostos ou específicos 

Neste projeto, podem existir tipos especiais, compostos e multivalorados. Como padrão de documentação, há uma definição norteadora para isso:

| Padrões de atributos especiais |
|----|
|(C) =  Composto (Tipo estruturado, composto de partes que juntas formam o atributo em si)|
|(M) =  Multivalorado (vários valores)|
|(MC) =  Multivalorado e Composto (lista / array etc de um tipo composto)|


### 3.1. Documento de identificação

Documento de identificação é uma estrutura composta de duas partes. A primeira é o tipo do documento e a segunda é o valor do documento em si.

> Atenção: neste template, "Documento de identificação" não significa documento JSON de banco NoSQL. É um tipo composto usado para representar CPF, CNPJ ou PASSAPORTE.

Atributos do tipo Documento de identificação:

| Atributo / Dado | Tipo | Obrigatório | Comentários | 
| :---: | :--- | :---: | :--- |
| tipo |  Elemento de lista enumerada |  Sim | Um dos valores possíveis: CPF, CNPJ ou PASSAPORTE. |
| valor | String | Sim | CPF validado contendo 11 números ou CNPJ contendo 14 caracteres ou, por fim, passaporte com no mínimo 5 caracteres. |

### 3.2. <Nome do tipo composto>

Descreva aqui um atributo composto ou uma estrutura reutilizável do projeto.

> Exemplo: Endereco é uma estrutura composta por logradouro, bairro, cidade, numero, complemento e cep. Caso uma entidade possua vários endereços, o atributo deve ser marcado como Multivalorado e Composto (MC).

| Atributo / Dado | Tipo conceitual | Obrigatório | Comentários |
| :---: | :--- | :---: | :--- |
| atributo_1 | Texto | Sim | Descrição do atributo interno. |
| atributo_2 | Número | Não | Descrição do atributo interno. |

## 4. Desdobramento físico ou tecnológico

Depois de descrever as entidades e seus atributos em nível conceitual, apresente como elas serão implementadas na tecnologia escolhida.

Esta seção pode ter uma representação relacional, uma representação em documentos JSON ou as duas, caso o objetivo seja comparar soluções.

### 4.1. Desdobramento para banco orientado a documentos

Use esta seção quando a entidade for representada em uma coleção de documentos, como em MongoDB, Firestore ou tecnologia semelhante.

| Entidade conceitual | Coleção | Estratégia |
| :--- | :--- | :--- |
| <Nome da Entidade> | <nome_da_colecao> | Indicar se os atributos compostos/multivalorados serão embutidos ou referenciados. |

Exemplo de documento:

```json
{
  "_id": "exemplo_001",
  "campo_simples": "valor",
  "campo_composto": {
    "parte_1": "valor",
    "parte_2": "valor"
  },
  "campo_multivalorado_composto": [
    {
      "parte_1": "valor",
      "parte_2": "valor"
    }
  ]
}
```

Justificativa:

> Explique por que os dados foram embutidos ou referenciados. Considere o padrão de consulta, o volume esperado, a frequência de atualização e a necessidade de consistência.

### 4.2. Desdobramento para banco relacional

Use esta seção quando a entidade for representada em tabelas relacionais.

### Tabela `<NOME_DA_TABELA>`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id | integer | Sim | PK | Identificador único. |
| campo_simples | varchar(100) | Sim |  | Campo simples da entidade. |

### Tabela `<NOME_DA_TABELA_RELACIONADA>`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id | integer | Sim | PK | Identificador único. |
| id_entidade_principal | integer | Sim | FK | Chave estrangeira para a tabela principal. |
| campo_do_tipo_composto | varchar(100) | Sim |  | Campo originado de atributo composto ou multivalorado. |

Justificativa:

> Explique por que o atributo composto ou multivalorado virou uma tabela própria. Considere normalização, consultas independentes, integridade referencial e volume de dados.

## 5. Consultas, índices e observações de arquitetura

Liste aqui as consultas mais importantes previstas para o sistema e os índices sugeridos.

| Consulta esperada | Campos envolvidos | Índices sugeridos | Observações |
| :--- | :--- | :--- | :--- |
| Buscar entidade por identificador | id | id | Consulta direta por chave. |
| Buscar entidade por atributo de negócio | campo | campo | Ajustar conforme a tecnologia escolhida. |
