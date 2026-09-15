# Laboratório de Modelagem Documental: Estudo de Caso Farm 3D

> **Nota importante:** atividade a ser realizada no laboratório por todas as turmas práticas, na semana do dia 17 ao dia 21/08.

A atividade prática consiste em criar, no MongoDB, as coleções e os documentos de exemplo que representem o domínio descrito.

A Aline é proprietária de uma pequena empresa de impressão 3D. Ela possui uma farm, ou seja, um conjunto de impressoras 3D usadas para produzir objetos impressos.

A empresa trabalha com três tipos principais de demanda:

1. Produtos próprios, planejados pela Aline para venda em sua loja física ou pela web.
2. Impressões sob encomenda, quando um cliente solicita a impressão de um objeto específico.
3. Modelos prontos licenciados, criados por outras pessoas ou empresas, mas que a Aline possui autorização para imprimir e vender.

O objetivo do sistema é apoiar o controle do negócio desde o planejamento de uma impressão até a produção, controle de estoque e cálculo de custos.

## Conceitos do domínio

Objeto 3D é qualquer item que possa ser produzido em uma impressora 3D.

Uma impressora 3D pode ser simples ou sofisticada, aberta ou fechada, de cor única ou multicolorida. Impressoras de cor única imprimem usando apenas um filamento por vez. Quando a peça exige outra cor, é necessária a troca manual do filamento. Impressoras multicoloridas podem trabalhar com vários carretéis e trocar de cor durante a impressão com pouca ou nenhuma intervenção manual.

Filamento é o material usado pela impressora 3D para produzir os objetos. Cada rolo de filamento possui características como tipo de material, cor, marca, peso original, quantidade disponível, fornecedor, data de compra e preço pago.

Exemplos de tipos de material:

1. PLA, sigla para ácido polilático, um material bastante usado em impressão 3D por ser fácil de imprimir.
2. ABS, sigla para acrilonitrila butadieno estireno, um material mais resistente, amplamente usado na indústria, mas que costuma exigir mais controle de temperatura.
3. PETG, sigla para politereftalato de etileno glicol, um material conhecido por combinar boa resistência com certa facilidade de impressão, não tão resistente quanto o ABS, mas não biodegradável como o PLA.

## Planejamento de impressão

Antes de iniciar uma impressão, a Aline precisa planejar o item que será produzido. Esse planejamento serve para estimar custo, tempo, materiais necessários e possíveis atividades manuais.

Por exemplo, a Aline pode decidir produzir bonecos colecionáveis do personagem XPTO para vender em sua loja. Antes de imprimir, ela precisa estimar:

1. Quantidade de filamento necessária.
2. Tipo e cor de cada filamento usado.
3. Tempo estimado de impressão.
4. Custo de energia das impressoras.
5. Custos adicionais de montagem, acabamento ou embalagem.
6. Valor ou cota de licença comercial, quando o modelo 3D exigir pagamento pelo direito de impressão e venda.
7. Possível preço de venda.

O mesmo raciocínio vale para uma impressão sob encomenda ou para um modelo licenciado. A diferença está na origem da demanda: produto próprio, pedido de cliente ou produto autorizado por licença.

## Dados informados pelo fatiador

Durante o planejamento, a Aline usa um fatiador. O fatiador é um software que analisa o modelo 3D e informa dados técnicos da impressão, como a quantidade estimada de filamento e o tempo estimado de impressão.

Suponha que a Aline queira imprimir um boneco XPTO usando filamento PLA nas cores branco, preto, azul e vermelho. O fatiador informa:

```text
Azul = 30g
Branco = 10g
Preto = 5g
Vermelho = 40g
```

O total de filamento estimado é:

```text
30g + 10g + 5g + 40g = 85g
```

O fatiador também informa o tempo estimado de impressão:

```text
03:00:00
```

Esse tempo representa apenas o período em que a impressora fica trabalhando. Ele não inclui atividades manuais, como troca de filamento, montagem, colagem, pintura, embalagem ou inspeção de qualidade.

## Custo do filamento

Para calcular o custo de uma impressão, a Aline precisa saber quanto custa cada grama de filamento usada.

Suponha que ela tenha comprado os seguintes rolos de 1kg:

```text
1 rolo de PLA preto, marca A = R$ 105
1 rolo de PLA azul, marca B = R$ 110
1 rolo de PLA branco, marca C = R$ 90
1 rolo de PLA vermelho, marca D = R$ 129
```

Neste exemplo, como cada rolo possui 1000g, o custo por grama seria:

```text
PLA preto = R$ 105 / 1000g = R$ 0,105 por grama
PLA azul = R$ 110 / 1000g = R$ 0,110 por grama
PLA branco = R$ 90 / 1000g = R$ 0,090 por grama
PLA vermelho = R$ 129 / 1000g = R$ 0,129 por grama
```

Com base nos dados do fatiador, o custo estimado de filamento para o boneco XPTO seria:

```text
Azul = 30g * R$ 0,110 = R$ 3,30
Branco = 10g * R$ 0,090 = R$ 0,90
Preto = 5g * R$ 0,105 = R$ 0,52
Vermelho = 40g * R$ 0,129 = R$ 5,16

Total de filamentos = R$ 9,88
```

## Regra de reposição do estoque

O preço usado no cálculo de custo não deve ser necessariamente o preço do rolo que está sendo consumido fisicamente naquele momento.

Para fins de planejamento e precificação, a Aline considera sempre o maior preço de referência conhecido para um filamento equivalente. Nesta atividade, considere filamentos equivalentes aqueles que possuem o mesmo tipo de material e a mesma cor.

Por exemplo:

```text
Compra de insumos 1: PLA branco, marca C, 1000g, R$ 90
Compra de insumos 2: PLA branco, marca D, 1000g, R$ 120
```

Mesmo que a impressão esteja consumindo o rolo comprado por R$ 90, o custo por grama considerado no planejamento deve usar o maior preço de referência conhecido:

```text
R$ 120 / 1000g = R$ 0,120 por grama
```

Essa regra existe porque, se a Aline vender produtos calculando o custo com base em um preço antigo mais barato, talvez não consiga repor o estoque quando precisar comprar novamente.

Para esta atividade, considere que o maior preço de referência conhecido pode vir de compras já realizadas, entradas de estoque, cotações de fornecedores ou valores informados manualmente pela Aline. Assim, mesmo que ela ainda não tenha comprado um novo rolo mais caro, pode registrar esse valor como referência de reposição para que o planejamento de custo não fique defasado.

O sistema deve registrar a origem desse preço de referência. Por exemplo: compra realizada, cotação de fornecedor, ajuste manual ou outra origem definida pela Aline.

## Estoque de filamentos

O sistema deve controlar os filamentos disponíveis para uso. Para cada compra ou entrada de filamento, é importante registrar dados como:

1. Tipo de material.
2. Cor.
3. Marca.
4. Peso original do rolo em gramas.
5. Quantidade atual disponível em gramas.
6. Preço pago.
7. Fornecedor.
8. Data da compra ou entrada.

O sistema também deve permitir saber se há quantidade suficiente para uma impressão planejada.

Por exemplo, se uma impressão exige 40g de PLA vermelho, o sistema deve conseguir verificar se existe pelo menos essa quantidade disponível em estoque para PLA vermelho.

## Custos adicionais

Nem tudo que compõe um produto sai diretamente da impressora.

Um chaveiro 3D, por exemplo, pode exigir:

1. Argola.
2. Correntinha.
3. Saquinho de embalagem.
4. Filipeta de papelão para lacrar a embalagem.
5. Valor ou cota de licença comercial, quando houver.

Outros produtos podem exigir cola, pintura, lixamento, montagem, acabamento manual ou inspeção de qualidade.

Esses custos também precisam ser considerados no planejamento, pois fazem parte do custo de produção do item.

## Escopo da atividade

Você não deve implementar uma aplicação, criar telas ou escrever código de backend.

Seu trabalho é propor uma modelagem documental e materializá-la no MongoDB, criando as coleções e inserindo documentos de exemplo capazes de representar o domínio descrito.

Como MongoDB permite diferentes estratégias de modelagem, não existe uma única resposta correta. O importante é que sua modelagem seja coerente, justificada e capaz de responder às necessidades do negócio.

## O que deve ser produzido no laboratório

Com base no modelo descritivo, produza no MongoDB:

1. As coleções MongoDB necessárias para representar o domínio.
2. Documentos de exemplo para cada coleção.
3. Campos suficientes para representar os dados descritos no estudo de caso.
4. Relacionamentos entre documentos, quando existirem, usando referências ou documentos embutidos.

Além da criação no MongoDB, registre uma breve justificativa contendo:

1. A finalidade de cada coleção.
2. As principais decisões de modelagem.
3. A explicação de quando uma informação foi embutida no mesmo documento e quando foi referenciada em outra coleção.

## Requisitos mínimos da modelagem

A modelagem criada deve contemplar, no mínimo:

1. Cadastro dos filamentos comprados pela Aline.
2. Controle da quantidade disponível de cada filamento em estoque.
3. Histórico de compras, entradas de filamento, cotações ou valores de referência informados manualmente.
4. Regra do maior preço de referência conhecido por tipo e cor de filamento.
5. Planejamento de uma impressão.
6. Quantidade estimada de filamento usada em gramas, por tipo e cor.
7. Tempo estimado de impressão informado pelo fatiador.
8. Custos adicionais que não saem da impressora.
9. Diferenciação entre produto próprio, impressão sob encomenda e modelo licenciado.

Você pode incluir outras coleções se considerar necessário, como clientes, pedidos, impressoras, fornecedores, produtos, modelos 3D, vendas ou ordens de produção. Caso inclua, justifique por que elas são úteis para a modelagem.

## Questões para orientar a análise

Ao elaborar sua resposta, pense nas seguintes perguntas:

1. Quais informações mudam com frequência e quais são mais estáveis?
2. Quais dados costumam ser consultados juntos?
3. Faz sentido embutir os itens de filamento usados dentro do planejamento de impressão?
4. Faz sentido manter o histórico de compras separado do saldo atual em estoque?
5. Como o sistema descobriria o maior preço de referência conhecido de um filamento PLA branco?
6. Como representar custos adicionais de produção?
7. Como diferenciar uma peça planejada para venda própria de uma impressão sob encomenda?
8. Quais campos seriam úteis para calcular o custo total estimado de uma impressão?
