# Dicionário de Dados: Locadora de Veículos Localiza/SA

> **Observação importante:** Este dicionário é um exemplo didático e não representa uma versão final, completa ou oficial de um banco de dados da Localiza/SA. O objetivo é compreender o processo de dicionarização: primeiro descrevemos entidades, atributos e regras do domínio; depois desdobramos essas entidades para estruturas físicas, relacionais ou documentais.

Este dicionário descreve dados de uma locadora de veículos. O cenário considera clientes, condutores autorizados, veículos, unidades de atendimento, locações, multas, sinistros e o controle de status do veículo ao longo do processo de locação.

## 1. Histórico de versões

| Data | Autor | Versão | Comentários |
| :---: | :---: | ---: | :--- |
| 13/08/2026 | Mateus Dias | 1.0.0-alfa1 | Versão inicial do dicionário de dados de locadora de veículos |

## 2. Entidades e atributos conceituais

### 2.1. Cliente

Cliente representa a pessoa física, pessoa jurídica ou cliente estrangeiro que contrata a locação de um veículo. O cliente é o responsável comercial e financeiro pela locação, ainda que outra pessoa seja cadastrada como condutora autorizada.

#### Atributos de Cliente

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 20, 340 | vazio, nulo | Código interno do cliente. |
| nome | Texto alfanumérico | Sim | Simples | João da Silva, XPTO Serviços LTDA | "", "A" | Nome completo ou razão social do cliente. |
| documento | Documento de identificação | Sim | Composto (C) | CPF, CNPJ ou PASSAPORTE válido | documento vazio ou inválido | Documento principal do cliente. |
| email | Texto | Não | Simples | cliente@email.com | email sem formato válido | E-mail para contato e envio de comprovantes. |
| telefone | Texto | Não | Simples | 19999990000 | texto sem número algum | Telefone principal de contato. |
| tipoCliente | Texto enumerado | Sim | Simples | PESSOA_FISICA, PESSOA_JURIDICA, ESTRANGEIRO | qualquer valor fora da lista | Classificação cadastral do cliente. |
| ativo | Booleano | Sim | Simples | true, false | sim, não | Indica se o cadastro está ativo. |

### 2.2. Condutor

Condutor representa uma pessoa autorizada a dirigir o veículo durante a locação. O cliente pode ser o próprio condutor ou pode indicar um ou mais condutores adicionais.

#### Atributos de Condutor

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 50, 200 | vazio, nulo | Código interno do condutor. |
| nome | Texto alfanumérico | Sim | Simples | Maria Oliveira | "", nome com poucos caracteres | Nome completo do condutor. |
| documento | Documento de identificação | Sim | Composto (C) | CPF ou PASSAPORTE válido | documento vazio ou inválido | Documento de identificação pessoal. |
| cnh | CNH | Não | Composto (C) | Número de CNH com categoria B | CNH vencida quando exigida | Carteira Nacional de Habilitação. Pode não existir para condutor estrangeiro, conforme regra de negócio. |
| dataNascimento | Data | Não | Simples | 1995-08-20 | data impossível | Pode ser usada para validação de idade mínima. |
| telefone | Texto | Não | Simples | 19988887777 | texto sem número algum | Telefone de contato do condutor. |
| ativo | Booleano | Sim | Simples | true, false | sim, não | Indica se o condutor está ativo no cadastro. |

### 2.3. Unidade

Unidade representa uma loja ou ponto de atendimento da locadora. Uma locação possui unidade de retirada e pode possuir unidade de devolução diferente.

#### Atributos de Unidade

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 12, 105 | vazio, nulo | Código interno da unidade. |
| nome | Texto | Sim | Simples | Campinas Cambuí, Aeroporto Viracopos | "", nome muito curto | Nome comercial da loja. |
| endereço | Endereço | Sim | Composto (C) | Rua, número, cidade e UF preenchidos | endereço sem cidade | Endereço físico da unidade. |
| telefone | Texto | Não | Simples | 1933334444 | texto sem número algum | Telefone da unidade. |
| ativa | Booleano | Sim | Simples | true, false | sim, não | Indica se a unidade está ativa para operações. |

### 2.4. Veículo

Veículo representa um automóvel pertencente à frota da locadora. O veículo pode estar aguardando emplacamento, disponível, alugado, devolvido ou em higienização.

#### Atributos de Veículo

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 300, 990 | vazio, nulo | Código interno do veículo. |
| placa | Texto | Não | Simples | ABC1D23 | placa vazia quando status exige placa | Pode ser nula enquanto o veículo estiver SEM_PLACA. |
| chassi | Texto | Sim | Simples | 9BWZZZ377VT004251 | vazio, nulo | Identificador do veículo. Deve ser único. |
| marca | Texto | Sim | Simples | Fiat, Volkswagen, Chevrolet | vazio | Marca do veículo. |
| modelo | Texto | Sim | Simples | Argo, Polo, Onix | vazio | Modelo do veículo. |
| anoModelo | Número inteiro | Sim | Simples | 2026 | 1800, texto | Ano modelo do veículo. |
| cor | Texto | Não | Simples | Branco, Prata, Preto | valor inconsistente | Cor predominante. |
| categoria | Texto enumerado | Sim | Simples | ECONOMICO, INTERMEDIARIO, SUV, PREMIUM | qualquer valor fora da lista | Categoria comercial do veículo. |
| quilometragemAtual | Número decimal | Sim | Simples | 12500.5 | número negativo | Quilometragem atual do veículo. |
| status | Status do veículo | Sim | Simples | SEM_PLACA, DISPONIVEL, ALUGADO, DEVOLVIDO, EM_HIGIENIZACAO, FURTADO, RETIRADO | qualquer valor fora da máquina de estados | Estado operacional atual do veículo. |
| unidadeAtual | Unidade | Não | Relacionamento | Unidade Campinas Cambuí | unidade inexistente | Unidade em que o veículo se encontra quando não está alugado. |

### 2.5. Locação

Locação representa o vínculo entre cliente e veículo durante um período de uso. Ela registra unidade de retirada, unidade prevista de devolução, data de retirada, data prevista de retorno, data efetiva de retorno, quilometragem e status da locação.

#### Atributos de Locação

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 1000, 5000 | vazio, nulo | Código interno da locação. |
| cliente | Cliente | Sim | Relacionamento | João da Silva | nulo | Cliente responsável pela contratação. |
| veículo | Veículo | Sim | Relacionamento | Veículo ABC1D23 | nulo | Veículo locado. |
| unidadeRetirada | Unidade | Sim | Relacionamento | Aeroporto Viracopos | nulo | Unidade em que o veículo foi retirado. |
| unidadeDevolucaoPrevista | Unidade | Sim | Relacionamento | Campinas Cambuí | nulo | Unidade em que o cliente informou que devolverá o veículo. |
| unidadeDevolucaoEfetiva | Unidade | Não | Relacionamento | Campinas Cambuí | unidade inexistente | Unidade onde o veículo foi efetivamente devolvido. |
| dataHoraRetirada | Data e hora | Sim | Simples | 2026-08-13 09:00:00 | data impossível | Momento da retirada do veículo. |
| dataHoraRetornoPrevisto | Data e hora | Sim | Simples | 2026-08-16 09:00:00 | data anterior à retirada | Retorno previsto no contrato. |
| dataHoraRetornoEfetivo | Data e hora | Não | Simples | 2026-08-16 10:30:00 | data anterior à retirada | Retorno efetivo, preenchido na devolução. |
| quilometragemRetirada | Número decimal | Sim | Simples | 12500.5 | número negativo | Quilometragem no momento da retirada. |
| quilometragemDevolucao | Número decimal | Não | Simples | 12810.0 | menor que a retirada | Quilometragem no momento da devolução. |
| valorPrevisto | Moeda | Sim | Simples | 850.00 | número negativo | Valor previsto da locação. |
| valorFinal | Moeda | Não | Simples | 930.00 | número negativo | Valor final após devolução, multas ou ajustes. |
| statusLocacao | Texto enumerado | Sim | Simples | ABERTA, FINALIZADA, CANCELADA | qualquer valor fora da lista | Situação da locação. |

### 2.6. Condutor da Locação

Condutor da Locação representa o vínculo entre uma locação e os condutores autorizados a dirigir o veículo. Uma locação pode ter um ou mais condutores.

#### Atributos de Condutor da Locação

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| locação | Locação | Sim | Relacionamento | Locação 1000 | nulo | Locação associada. |
| condutor | Condutor | Sim | Relacionamento | Maria Oliveira | nulo | Condutor autorizado. |
| principal | Booleano | Sim | Simples | true, false | sim, não | Indica se é o condutor principal. |
| dataAutorizacao | Data | Sim | Simples | 2026-08-13 | data impossível | Data em que o condutor foi autorizado. |

### 2.7. Multa

Multa representa uma infração de trânsito vinculada ao veículo durante ou após uma locação. A multa pode ser associada à locação e, quando possível, a um condutor.

#### Atributos de Multa

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 77, 900 | vazio, nulo | Código interno da multa. |
| locação | Locação | Não | Relacionamento | Locação 1000 | locação inexistente | Locação associada, quando identificável. |
| veículo | Veículo | Sim | Relacionamento | Veículo ABC1D23 | nulo | Veículo relacionado à multa. |
| condutor | Condutor | Não | Relacionamento | Maria Oliveira | condutor inexistente | Condutor provável, quando identificado. |
| dataHoraInfracao | Data e hora | Sim | Simples | 2026-08-14 16:20:00 | data impossível | Momento da infração. |
| descricao | Texto | Sim | Simples | Excesso de velocidade | vazio | Descrição da infração. |
| valor | Moeda | Sim | Simples | 195.23 | número negativo | Valor da multa. |
| statusMulta | Texto enumerado | Sim | Simples | RECEBIDA, EM_ANALISE, REPASSADA, PAGA, CANCELADA | qualquer valor fora da lista | Situação administrativa da multa. |

### 2.8. Sinistro

Sinistro representa uma ocorrência envolvendo dano, acidente, furto, roubo ou evento relevante com o veículo. O sinistro pode ocorrer durante uma locação ou fora dela.

#### Atributos de Sinistro

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 40, 120 | vazio, nulo | Código interno do sinistro. |
| locação | Locação | Não | Relacionamento | Locação 1000 | locação inexistente | Locação associada, quando houver. |
| veículo | Veículo | Sim | Relacionamento | Veículo ABC1D23 | nulo | Veículo envolvido. |
| condutor | Condutor | Não | Relacionamento | Maria Oliveira | condutor inexistente | Condutor envolvido, quando identificado. |
| tipoSinistro | Texto enumerado | Sim | Simples | COLISAO, FURTO, ROUBO, DANO, OUTRO_EVENTO | qualquer valor fora da lista | Classificação do sinistro. |
| dataHoraOcorrencia | Data e hora | Sim | Simples | 2026-08-14 20:10:00 | data impossível | Momento da ocorrência. |
| descricao | Texto | Sim | Simples | Colisão leve na lateral direita | vazio | Descrição do ocorrido. |
| boletimOcorrencia | Texto | Não | Simples | BO-123456 | vazio quando exigido | Número ou referência do boletim de ocorrência. |
| valorEstimado | Moeda | Não | Simples | 2500.00 | número negativo | Valor estimado do prejuízo. |
| statusSinistro | Texto enumerado | Sim | Simples | ABERTO, EM_ANALISE, APROVADO, RECUSADO, ENCERRADO | qualquer valor fora da lista | Situação do sinistro. |

### 2.9. Histórico de Status do Veículo

Histórico de Status do Veículo registra as mudanças de estado de um veículo ao longo do tempo. Essa entidade é útil para auditoria e para entender o ciclo operacional da frota.

#### Atributos de Histórico de Status do Veículo

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 200, 600 | vazio, nulo | Código interno do registro histórico. |
| veículo | Veículo | Sim | Relacionamento | Veículo ABC1D23 | nulo | Veículo cujo status foi alterado. |
| statusAnterior | Status do veículo | Não | Simples | SEM_PLACA | valor fora da máquina de estados | Status anterior. Pode ser nulo no primeiro registro. |
| statusNovo | Status do veículo | Sim | Simples | DISPONIVEL | valor fora da máquina de estados | Novo status do veículo. |
| dataHoraAlteracao | Data e hora | Sim | Simples | 2026-08-13 08:30:00 | data impossível | Momento da alteração. |
| motivo | Texto | Não | Simples | Veículo devolvido e enviado para higienização | vazio | Justificativa da mudança de status. |

## 3. Tipos especiais, compostos ou específicos

### 3.1. Documento de identificação

Documento de identificação é uma estrutura composta por tipo e valor. Para clientes, os tipos previstos são CPF, CNPJ e PASSAPORTE. Para condutores, os tipos previstos são CPF e PASSAPORTE.

| Atributo / Dado | Tipo conceitual | Obrigatório | Comentários |
| :--- | :--- | :---: | :--- |
| tipo | Texto enumerado | Sim | Valores previstos: CPF, CNPJ ou PASSAPORTE para clientes; CPF ou PASSAPORTE para condutores. |
| valor | Texto | Sim | Valor do documento. Deve respeitar a regra do tipo informado quando houver validação disponível. |

### 3.2. CNH

CNH representa a Carteira Nacional de Habilitação do condutor.

| Atributo / Dado | Tipo conceitual | Obrigatório | Comentários |
| :--- | :--- | :---: | :--- |
| numero | Texto | Sim | Número da CNH. |
| categoria | Texto enumerado | Sim | Valores previstos: A, B, AB, C, D, E. |
| validade | Data | Sim | Data de validade da CNH. |

### 3.3. Endereço

Endereço representa a localização física de uma unidade da locadora.

| Atributo / Dado | Tipo conceitual | Obrigatório | Comentários |
| :--- | :--- | :---: | :--- |
| logradouro | Texto | Sim | Rua, avenida ou via. |
| numero | Texto | Sim | Número do imóvel. |
| complemento | Texto | Não | Complemento, sala ou referência. |
| bairro | Texto | Sim | Bairro. |
| cidade | Texto | Sim | Cidade. |
| uf | Texto | Sim | Unidade federativa. |
| cep | Texto | Sim | Código postal. |

### 3.4. Status do veículo

Status do veículo representa uma máquina de estados simplificada para controlar a situação operacional do veículo.

| Status | Descrição |
| :--- | :--- |
| SEM_PLACA | Veículo cadastrado, mas aguardando emplacamento. |
| DISPONIVEL | Veículo disponível para locação. |
| ALUGADO | Veículo está em uma locação aberta. |
| DEVOLVIDO | Veículo foi devolvido pelo cliente, mas ainda não está pronto para nova locação. |
| EM_HIGIENIZACAO | Veículo está em lavagem ou preparação para voltar à frota disponível. |
| FURTADO | Veículo foi furtado e precisa ser mantido no banco para histórico e auditoria. |
| RETIRADO | Veículo foi retirado da frota de locação por alta quilometragem, venda, repasse ou decisão administrativa. |

#### Transições sugeridas

| Status atual | Próximo status permitido | Observação |
| :--- | :--- | :--- |
| SEM_PLACA | DISPONIVEL | Após emplacamento. |
| DISPONIVEL | ALUGADO | Quando uma locação é aberta e o veículo é retirado. |
| ALUGADO | DEVOLVIDO | Quando o cliente devolve o veículo. |
| DEVOLVIDO | EM_HIGIENIZACAO | Após conferência inicial da devolução. |
| EM_HIGIENIZACAO | DISPONIVEL | Após limpeza e liberação operacional. |
| ALUGADO | FURTADO | Quando houver registro de furto durante a locação. |
| DISPONIVEL | RETIRADO | Quando o veículo for retirado da frota por alta quilometragem, venda ou repasse. |
| DEVOLVIDO | RETIRADO | Quando, após devolução e conferência, a empresa decidir retirar o veículo da frota. |

### 3.5. Padrões de atributos especiais

| Código | Significado | Uso no dicionário |
| :---: | :--- | :--- |
| C | Composto | Usado quando um atributo possui partes internas. |
| M | Multivalorado | Usado quando um atributo pode ter vários valores. |
| MC | Multivalorado e composto | Usado quando há uma lista de estruturas compostas. |

## 4. Desdobramento físico ou tecnológico

### 4.1. Desdobramento para banco relacional

O modelo relacional separa as entidades em tabelas e utiliza chaves estrangeiras para representar os vínculos entre cliente, veículo, unidade, locação, condutores, multas e sinistros.

### Tabela `CLIENTES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_cliente | integer | Sim | PK | Código único do cliente. |
| nome | varchar(150) | Sim |  | Nome completo ou razão social. |
| documento_tipo | varchar(20) | Sim |  | Valores: CPF, CNPJ, PASSAPORTE. |
| documento_valor | varchar(30) | Sim | UK | Valor do documento. |
| email | varchar(150) | Não |  | E-mail de contato. |
| telefone | varchar(30) | Não |  | Telefone principal. |
| tipo_cliente | varchar(30) | Sim |  | Valores: PESSOA_FISICA, PESSOA_JURIDICA, ESTRANGEIRO. |
| ativo | boolean | Sim |  | Indica se o cadastro está ativo. |

### Tabela `CONDUTORES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_condutor | integer | Sim | PK | Código único do condutor. |
| nome | varchar(150) | Sim |  | Nome completo do condutor. |
| documento_tipo | varchar(20) | Sim |  | Valores: CPF, PASSAPORTE. |
| documento_valor | varchar(30) | Sim | UK | Valor do documento. |
| cnh_numero | varchar(30) | Não |  | Número da CNH. |
| cnh_categoria | varchar(5) | Não |  | Categoria da CNH. |
| cnh_validade | date | Não |  | Validade da CNH. |
| data_nascimento | date | Não |  | Data de nascimento. |
| telefone | varchar(30) | Não |  | Telefone de contato. |
| ativo | boolean | Sim |  | Indica se o condutor está ativo. |

### Tabela `UNIDADES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_unidade | integer | Sim | PK | Código único da unidade. |
| nome | varchar(120) | Sim |  | Nome comercial da unidade. |
| logradouro | varchar(150) | Sim |  | Logradouro. |
| numero | varchar(20) | Sim |  | Número. |
| complemento | varchar(100) | Não |  | Complemento. |
| bairro | varchar(100) | Sim |  | Bairro. |
| cidade | varchar(100) | Sim |  | Cidade. |
| uf | char(2) | Sim |  | Unidade federativa. |
| cep | char(8) | Sim |  | Código postal. |
| telefone | varchar(30) | Não |  | Telefone da unidade. |
| ativa | boolean | Sim |  | Indica se a unidade está ativa. |

### Tabela `VEICULOS`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_veiculo | integer | Sim | PK | Código único do veículo. |
| placa | varchar(10) | Não | UK | Pode ser nula enquanto o veículo estiver SEM_PLACA. |
| chassi | varchar(30) | Sim | UK | Identificador único do veículo. |
| marca | varchar(60) | Sim |  | Marca. |
| modelo | varchar(80) | Sim |  | Modelo. |
| ano_modelo | integer | Sim |  | Ano modelo. |
| cor | varchar(40) | Não |  | Cor predominante. |
| categoria | varchar(30) | Sim |  | Valores: ECONOMICO, INTERMEDIARIO, SUV, PREMIUM. |
| quilometragem_atual | decimal(10,1) | Sim |  | Quilometragem atual. |
| status | varchar(30) | Sim |  | Valores da máquina de estados do veículo. |
| id_unidade_atual | integer | Não | FK | Referência para `UNIDADES`, quando aplicável. |

### Tabela `LOCACOES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_locacao | integer | Sim | PK | Código único da locação. |
| id_cliente | integer | Sim | FK | Referência para `CLIENTES`. |
| id_veiculo | integer | Sim | FK | Referência para `VEICULOS`. |
| id_unidade_retirada | integer | Sim | FK | Unidade onde o veículo foi retirado. |
| id_unidade_devolucao_prevista | integer | Sim | FK | Unidade prevista para devolução. |
| id_unidade_devolucao_efetiva | integer | Não | FK | Unidade efetiva de devolução. |
| data_hora_retirada | timestamp | Sim |  | Data e hora de retirada. |
| data_hora_retorno_previsto | timestamp | Sim |  | Data e hora previstas de retorno. |
| data_hora_retorno_efetivo | timestamp | Não |  | Data e hora efetivas de retorno. |
| quilometragem_retirada | decimal(10,1) | Sim |  | Quilometragem na retirada. |
| quilometragem_devolucao | decimal(10,1) | Não |  | Quilometragem na devolução. |
| valor_previsto | decimal(10,2) | Sim |  | Valor previsto. |
| valor_final | decimal(10,2) | Não |  | Valor final. |
| status_locacao | varchar(20) | Sim |  | Valores: ABERTA, FINALIZADA, CANCELADA. |

### Tabela `LOCACOES_CONDUTORES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_locacao_condutor | integer | Sim | PK | Código único do vínculo. |
| id_locacao | integer | Sim | FK | Referência para `LOCACOES`. |
| id_condutor | integer | Sim | FK | Referência para `CONDUTORES`. |
| principal | boolean | Sim |  | Indica se é o condutor principal. |
| data_autorizacao | date | Sim |  | Data da autorização. |

### Tabela `MULTAS`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_multa | integer | Sim | PK | Código único da multa. |
| id_locacao | integer | Não | FK | Referência para `LOCACOES`, quando identificável. |
| id_veiculo | integer | Sim | FK | Referência para `VEICULOS`. |
| id_condutor | integer | Não | FK | Referência para `CONDUTORES`, quando identificado. |
| data_hora_infracao | timestamp | Sim |  | Data e hora da infração. |
| descricao | varchar(500) | Sim |  | Descrição da infração. |
| valor | decimal(10,2) | Sim |  | Valor da multa. |
| status_multa | varchar(20) | Sim |  | Valores: RECEBIDA, EM_ANALISE, REPASSADA, PAGA, CANCELADA. |

### Tabela `SINISTROS`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_sinistro | integer | Sim | PK | Código único do sinistro. |
| id_locacao | integer | Não | FK | Referência para `LOCACOES`, quando houver. |
| id_veiculo | integer | Sim | FK | Referência para `VEICULOS`. |
| id_condutor | integer | Não | FK | Referência para `CONDUTORES`, quando identificado. |
| tipo_sinistro | varchar(30) | Sim |  | Valores: COLISAO, FURTO, ROUBO, DANO, OUTRO_EVENTO. |
| data_hora_ocorrencia | timestamp | Sim |  | Data e hora da ocorrência. |
| descricao | varchar(1000) | Sim |  | Descrição do ocorrido. |
| boletim_ocorrencia | varchar(100) | Não |  | Número ou referência do boletim. |
| valor_estimado | decimal(10,2) | Não |  | Valor estimado do prejuízo. |
| status_sinistro | varchar(20) | Sim |  | Valores: ABERTO, EM_ANALISE, APROVADO, RECUSADO, ENCERRADO. |

### Tabela `HISTORICO_STATUS_VEICULOS`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_historico_status_veiculo | integer | Sim | PK | Código único do histórico. |
| id_veiculo | integer | Sim | FK | Referência para `VEICULOS`. |
| status_anterior | varchar(30) | Não |  | Status anterior. |
| status_novo | varchar(30) | Sim |  | Novo status. |
| data_hora_alteracao | timestamp | Sim |  | Data e hora da alteração. |
| motivo | varchar(500) | Não |  | Motivo da alteração. |

### 4.2. Desdobramento para banco não relacional orientado a documentos: MongoDB

> Caso você esteja estudando bancos não relacionais, esta seção ajuda a visualizar documentos de exemplo e simulações de documentos reais para o mesmo domínio.

Em MongoDB, uma locação pode ser modelada como documento principal contendo um resumo do cliente, veículo, unidades e condutores autorizados. Multas e sinistros podem ficar em coleções próprias, pois podem surgir posteriormente, ser consultados de forma independente e ter ciclo administrativo próprio.

#### Coleções sugeridas

| Coleção | Finalidade | Estratégia documental |
| :--- | :--- | :--- |
| `clientes` | Armazenar clientes | Documento próprio, consultado por documento de identificação. |
| `condutores` | Armazenar condutores | Documento próprio, reutilizável em várias locações. |
| `unidades` | Armazenar lojas de retirada e devolução | Documento próprio. |
| `veiculos` | Armazenar frota e status atual | Documento próprio, com histórico resumido opcional. |
| `locacoes` | Armazenar contratos/eventos de locação | Embute resumos de cliente, veículo, unidades e condutores. |
| `multas` | Armazenar multas | Documento próprio, referenciando locação e veículo. |
| `sinistros` | Armazenar sinistros | Documento próprio, referenciando locação e veículo. |

#### Exemplo de documento da coleção `locacoes`

```json
{
  "_id": "locacao_1000",
  "codigo": 1000,
  "cliente": {
    "codigo": 10,
    "nome": "João da Silva",
    "documento": {
      "tipo": "CPF",
      "valor": "12345678901"
    },
    "tipoCliente": "PESSOA_FISICA"
  },
  "veiculo": {
    "codigo": 300,
    "placa": "ABC1D23",
    "chassi": "9BWZZZ377VT004251",
    "marca": "Fiat",
    "modelo": "Argo",
    "categoria": "ECONOMICO"
  },
  "unidadeRetirada": {
    "codigo": 5,
    "nome": "Aeroporto Viracopos"
  },
  "unidadeDevolucaoPrevista": {
    "codigo": 8,
    "nome": "Campinas Cambuí"
  },
  "unidadeDevolucaoEfetiva": null,
  "dataHoraRetirada": "2026-08-13T09:00:00-03:00",
  "dataHoraRetornoPrevisto": "2026-08-16T09:00:00-03:00",
  "dataHoraRetornoEfetivo": null,
  "quilometragemRetirada": 12500.5,
  "quilometragemDevolucao": null,
  "valorPrevisto": 850.00,
  "valorFinal": null,
  "statusLocacao": "ABERTA",
  "condutores": [
    {
      "codigo": 20,
      "nome": "João da Silva",
      "documento": {
        "tipo": "CPF",
        "valor": "12345678901"
      },
      "principal": true,
      "dataAutorizacao": "2026-08-13"
    },
    {
      "codigo": 21,
      "nome": "Maria Oliveira",
      "documento": {
        "tipo": "CPF",
        "valor": "98765432100"
      },
      "principal": false,
      "dataAutorizacao": "2026-08-13"
    }
  ]
}
```

#### Exemplo de documento da coleção `veiculos`

```json
{
  "_id": "veiculo_300",
  "codigo": 300,
  "placa": "ABC1D23",
  "chassi": "9BWZZZ377VT004251",
  "marca": "Fiat",
  "modelo": "Argo",
  "anoModelo": 2026,
  "cor": "Branco",
  "categoria": "ECONOMICO",
  "quilometragemAtual": 12500.5,
  "status": "ALUGADO",
  "unidadeAtual": null,
  "historicoStatus": [
    {
      "statusAnterior": "SEM_PLACA",
      "statusNovo": "DISPONIVEL",
      "dataHoraAlteracao": "2026-07-20T10:00:00-03:00",
      "motivo": "Veículo emplacado"
    },
    {
      "statusAnterior": "DISPONIVEL",
      "statusNovo": "ALUGADO",
      "dataHoraAlteracao": "2026-08-13T09:00:00-03:00",
      "motivo": "Locação 1000 aberta"
    }
  ]
}
```

#### Exemplo de documento da coleção `multas`

```json
{
  "_id": "multa_77",
  "codigo": 77,
  "locacao": {
    "codigo": 1000
  },
  "veiculo": {
    "codigo": 300,
    "placa": "ABC1D23"
  },
  "condutor": {
    "codigo": 20,
    "nome": "João da Silva"
  },
  "dataHoraInfracao": "2026-08-14T16:20:00-03:00",
  "descricao": "Excesso de velocidade",
  "valor": 195.23,
  "statusMulta": "RECEBIDA"
}
```

#### Exemplo de documento da coleção `sinistros`

```json
{
  "_id": "sinistro_40",
  "codigo": 40,
  "locacao": {
    "codigo": 1000
  },
  "veiculo": {
    "codigo": 300,
    "placa": "ABC1D23",
    "marca": "Fiat",
    "modelo": "Argo"
  },
  "condutor": {
    "codigo": 21,
    "nome": "Maria Oliveira"
  },
  "tipoSinistro": "COLISAO",
  "dataHoraOcorrencia": "2026-08-14T20:10:00-03:00",
  "descricao": "Colisão leve na lateral direita",
  "boletimOcorrencia": "BO-123456",
  "valorEstimado": 2500.00,
  "statusSinistro": "ABERTO"
}
```

#### Observações sobre a modelagem documental

- A coleção `locacoes` embute dados resumidos porque a consulta de contrato costuma precisar ver cliente, veículo, unidades e condutores juntos.
- Multas e sinistros ficam separados porque podem ter ciclo de vida próprio e ser consultados por equipes diferentes.
- O histórico de status pode ser embutido em `veiculos` se o volume for pequeno; se crescer muito, pode virar uma coleção separada.
- A duplicação de resumos, como nome do cliente ou placa do veículo, pode ser aceitável para preservar o estado de uma locação no momento do contrato.
- Bons candidatos a índice: `clientes.documento.valor`, `condutores.documento.valor`, `veiculos.placa`, `veiculos.chassi`, `veiculos.status`, `locacoes.cliente.documento.valor`, `locacoes.veiculo.placa`, `locacoes.statusLocacao`, `multas.locacao.codigo` e `sinistros.locacao.codigo`.

### 4.3. Justificativa dos desdobramentos

No modelo relacional, a separação em tabelas facilita integridade referencial, evita duplicação excessiva e permite controlar vínculos muitos-para-muitos, como locações com múltiplos condutores.

No modelo orientado a documentos, a locação pode ser tratada como agregado principal, reunindo os dados necessários para consultar rapidamente um contrato. Multas, sinistros e veículos permanecem em coleções próprias porque possuem consultas e ciclos de vida independentes.

## 5. Consultas, índices e observações de arquitetura

### 5.1. Consultas esperadas

| Consulta esperada | Campos envolvidos | Índices sugeridos | Observações |
| :--- | :--- | :--- | :--- |
| Buscar cliente por documento | `CLIENTES.documento_tipo`, `CLIENTES.documento_valor` | `idx_clientes_documento` | Consulta comum antes de abrir locação. |
| Buscar condutor por documento | `CONDUTORES.documento_tipo`, `CONDUTORES.documento_valor` | `idx_condutores_documento` | Validação de condutor autorizado. |
| Buscar veículo por placa | `VEICULOS.placa` | `idx_veiculos_placa` | Consulta operacional da frota. |
| Buscar veículo por chassi | `VEICULOS.chassi` | `idx_veiculos_chassi` | Útil antes do emplacamento. |
| Buscar veículos por status | `VEICULOS.status` | `idx_veiculos_status` | Consulta de disponibilidade da frota. |
| Buscar locações abertas | `LOCACOES.status_locacao` | `idx_locacoes_status` | Consulta operacional. |
| Buscar locações por cliente | `LOCACOES.id_cliente` | `idx_locacoes_cliente` | Histórico de locações do cliente. |
| Buscar locações por veículo | `LOCACOES.id_veiculo` | `idx_locacoes_veiculo` | Histórico de uso do veículo. |
| Buscar multas por locação | `MULTAS.id_locacao` | `idx_multas_locacao` | Apoia cobrança e repasse. |
| Buscar sinistros por veículo | `SINISTROS.id_veiculo` | `idx_sinistros_veiculo` | Apoia análise de histórico do veículo. |

### 5.2. Regras e observações

- Cliente deve possuir documento de identificação válido.
- Cliente estrangeiro pode ser cadastrado com PASSAPORTE.
- Veículo pode estar sem placa apenas quando seu status for `SEM_PLACA`.
- Veículo em status `DISPONIVEL` pode ser locado.
- Ao abrir uma locação, o veículo deve mudar para `ALUGADO`.
- Ao registrar devolução, o veículo deve mudar para `DEVOLVIDO`.
- Após conferência, o veículo pode ir para `EM_HIGIENIZACAO`.
- Após higienização, o veículo pode voltar para `DISPONIVEL`.
- Veículo furtado deve mudar para `FURTADO`, mas deve permanecer cadastrado para histórico, auditoria, multas, sinistros e consultas futuras.
- Veículo retirado da frota deve mudar para `RETIRADO`, sem exclusão física do cadastro.
- Uma locação deve possuir pelo menos um condutor autorizado.
- Multas e sinistros podem existir mesmo quando a associação com uma locação não for imediatamente conhecida.
- O histórico de status do veículo não deve ser removido fisicamente em uso normal, pois compõe trilha de auditoria.
