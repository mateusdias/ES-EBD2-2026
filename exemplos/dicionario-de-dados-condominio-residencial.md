# Dicionário de Dados: Condomínio Residencial de Bloco Único

> **Observação importante:** Este dicionário está longe de ser uma versão real de um banco de dados que possa ser usado em um projeto de gestão condominial. O propósito deste exemplo é o entendimento do processo de dicionarização. Então, quanto mais entidades, mais complexo fica o dicionário, mais longo e detalhado o documento. O objetivo é compreender os passos através de um exemplo que vai desde a descrição de cada entidade até o desdobramento físico e estratégia de implementação em um banco relacional ou não relacional (a depender da situação).

Este dicionário descreve os dados de um condomínio residencial simples, composto por um único bloco predial. O objetivo é documentar as entidades necessárias para controlar unidades, condôminos, moradores, visitantes, veículos e registros de entrada e saída na portaria.

Neste primeiro momento, o desdobramento tecnológico será feito apenas para tabelas relacionais.

## 1. Histórico de versões

| Data | Autor | Versão | Comentários |
| :---: | :---: | ---: | :--- |
| 13/08/2026 | Mateus Dias | 1.0.0-alfa1 | Versão inicial do dicionário de dados do condomínio residencial |

## 2. Entidades e atributos conceituais

### 2.1. Unidade

Unidade representa o apartamento existente no condomínio. Como o condomínio possui apenas um bloco, a unidade pode ser identificada principalmente pelo número do apartamento e por informações complementares, como andar, situação e fração ideal, caso o projeto deseje registrar dados administrativos.

#### Atributos de Unidade

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 25, 103 | vazio, nulo | Código interno da unidade. |
| numero | Texto alfanumérico | Sim | Simples | 101, 1202, COB1 | vazio, nulo | Número ou código do apartamento. Deve ser único no condomínio. |
| andar | Número inteiro | Não | Simples | 1, 2, 12 | -1, texto | Andar em que a unidade está localizada. |
| situacao | Texto enumerado | Sim | Simples | OCUPADA, VAGA, EM_REFORMA, INATIVA | qualquer valor fora da lista | Indica a situação administrativa ou operacional da unidade. |
| observacoes | Texto | Não | Simples | Unidade em reforma até setembro | texto ofensivo ou inconsistente | Campo livre para observações administrativas. |

### 2.2. Condômino

Condômino representa a pessoa responsável financeiramente ou juridicamente por uma unidade. Neste dicionário, o condômino pode ser proprietário ou locador do imóvel, ou seja, a pessoa associada ao pagamento do condomínio.

Um condômino pode estar associado a uma ou mais unidades, e uma unidade pode ter mais de um condômino responsável, dependendo da regra administrativa adotada pelo condomínio.

#### Atributos de Condômino

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 78, 203 | vazio, nulo | Código interno do condômino. |
| nome | Texto alfanumérico | Sim | Simples | Maria Oliveira, João Pereira | "", "A" | Nome completo ou razão social, caso o responsável seja pessoa jurídica. |
| documento | Documento de identificação | Sim | Composto (C) | CPF, CNPJ ou PASSAPORTE válido | documento vazio ou inválido | Documento principal do condômino. |
| email | Texto | Não | Simples | maria@email.com | email sem formato válido | Usado para contato administrativo. |
| telefone | Texto | Não | Simples | 19999990000 | texto sem número algum | Telefone principal de contato. |
| tipoResponsavel | Texto enumerado | Sim | Simples | PROPRIETARIO, LOCADOR | qualquer valor fora da lista | Indica a natureza do vínculo financeiro ou jurídico. |
| ativo | Booleano | Sim | Simples | true, false | sim, não | Indica se o condômino está ativo no cadastro. |

### 2.3. Morador

Morador representa uma pessoa que reside em uma unidade do condomínio. O morador pode ser também condômino, mas isso não é obrigatório. Por exemplo, um filho, cônjuge, familiar ou inquilino pode morar na unidade sem ser o responsável pelo pagamento do condomínio.

#### Atributos de Morador

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 40, 91 | vazio, nulo | Código interno do morador. |
| nome | Texto alfanumérico | Sim | Simples | Ana Souza, Pedro Lima | "", nome com poucos caracteres | Nome completo do morador. |
| documento | Documento de identificação | Sim | Composto (C) | CPF, RG ou PASSAPORTE válido | documento vazio ou inválido | Documento usado para identificação na portaria. |
| dataNascimento | Data | Não | Simples | 2001-04-12 | data impossível | Pode ser usado para diferenciar pessoas com nomes semelhantes. |
| telefone | Texto | Não | Simples | 19988887777 | texto sem número algum | Telefone para contato. |
| fotoReconhecimentoFacial | Arquivo ou URL | Não | Simples | /fotos/moradores/123.jpg | caminho inválido | Referência para a foto usada em reconhecimento facial. O arquivo em si pode ser armazenado fora do banco relacional. |
| ativo | Booleano | Sim | Simples | true, false | sim, não | Indica se o morador ainda reside no condomínio. |

> Sobre biometria facial: há diferentes estratégias para armazenar a foto usada no reconhecimento. Uma possibilidade é guardar o arquivo como dado binário no próprio banco de dados. Outra é armazenar a imagem fora do banco, em um serviço de arquivos ou armazenamento de objetos, mantendo no banco apenas o caminho, URL ou identificador do arquivo. Neste dicionário, adotamos a segunda estratégia por simplicidade didática.

### 2.4. Visitante

Visitante representa uma pessoa que não mora no condomínio, mas pode acessar temporariamente o prédio. Visitantes devem ser cadastrados para que a portaria tenha histórico de acessos, autorizações e identificação.

#### Atributos de Visitante

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 55, 120 | vazio, nulo | Código interno do visitante. |
| nome | Texto alfanumérico | Sim | Simples | Carlos Mendes, Patrícia Alves | "", nome com poucos caracteres | Nome completo informado no cadastro ou na portaria. |
| documento | Documento de identificação | Não | Composto (C) | CPF, RG, CNH ou PASSAPORTE | documento inválido | Pode ser obrigatório conforme regra do condomínio. |
| telefone | Texto | Não | Simples | 19977776666 | texto sem número algum | Telefone de contato do visitante. |
| observacoes | Texto | Não | Simples | Prestador de serviço recorrente | texto ofensivo ou inconsistente | Observações para apoio à portaria. |

### 2.5. Veículo

Veículo representa qualquer veículo que possa acessar a garagem ou área interna do condomínio. Ele pode pertencer a um morador ou a um visitante.

#### Atributos de Veículo

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 22, 300 | vazio, nulo | Código interno do veículo. |
| placa | Texto | Sim | Simples | ABC1D23, ABC1234 | placa vazia | Placa do veículo. Deve ser única quando conhecida. |
| marca | Texto | Não | Simples | Toyota, Honda, Fiat | valor vazio quando exigido | Marca do veículo. |
| modelo | Texto | Não | Simples | Corolla, Civic, Argo | valor vazio quando exigido | Modelo do veículo. |
| cor | Texto | Não | Simples | Prata, Preto, Branco | valor inconsistente | Cor predominante do veículo. |
| tipoVeiculo | Texto enumerado | Sim | Simples | AUTOMOVEL, MOTOCICLETA, BICICLETA | qualquer valor fora da lista | Classificação do veículo. Triciclos devem ser cadastrados como MOTOCICLETA. |
| tipoVinculo | Texto enumerado | Sim | Simples | MORADOR, VISITANTE | qualquer valor fora da lista | Indica se o veículo é de morador ou visitante. |
| ativo | Booleano | Sim | Simples | true, false | sim, não | Indica se o veículo continua autorizado ou cadastrado. |

### 2.6. Vínculo Condômino-Unidade

Vínculo Condômino-Unidade representa a associação entre um condômino e uma unidade. Essa entidade é necessária porque um condômino pode ser responsável por mais de uma unidade e uma unidade pode ter mais de um condômino.

#### Atributos de Vínculo Condômino-Unidade

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| condômino | Condômino | Sim | Relacionamento | Maria Oliveira | nulo | Condômino associado. |
| unidade | Unidade | Sim | Relacionamento | Unidade 101 | nulo | Unidade associada. |
| dataInicio | Data | Sim | Simples | 2026-08-01 | data impossível | Data inicial do vínculo. |
| dataFim | Data | Não | Simples | 2027-01-15 | data anterior ao início | Data final do vínculo, quando houver. |
| principal | Booleano | Sim | Simples | true, false | sim, não | Indica se é o principal responsável pela unidade. |

### 2.7. Vínculo Morador-Unidade

Vínculo Morador-Unidade representa a associação entre uma pessoa moradora e a unidade em que ela reside. Essa entidade permite registrar histórico de moradores por unidade.

#### Atributos de Vínculo Morador-Unidade

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| morador | Morador | Sim | Relacionamento | Ana Souza | nulo | Morador associado. |
| unidade | Unidade | Sim | Relacionamento | Unidade 302 | nulo | Unidade associada. |
| dataEntradaMoradia | Data | Sim | Simples | 2026-02-10 | data impossível | Data em que o morador passou a residir na unidade. |
| dataSaidaMoradia | Data | Não | Simples | 2026-12-20 | data anterior à entrada | Data em que o morador deixou a unidade. |
| tipoMorador | Texto enumerado | Sim | Simples | PROPRIETARIO_RESIDENTE, INQUILINO, FAMILIAR, DEPENDENTE, OUTRO | qualquer valor fora da lista | Papel do morador na unidade. |

### 2.8. Vínculo Veículo-Morador

Vínculo Veículo-Morador representa a associação entre um veículo e um morador. Ele permite controlar quais veículos pertencem ou estão autorizados para moradores específicos.

#### Atributos de Vínculo Veículo-Morador

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| veículo | Veículo | Sim | Relacionamento | ABC1D23 | nulo | Veículo associado ao morador. |
| morador | Morador | Sim | Relacionamento | Ana Souza | nulo | Morador associado ao veículo. |
| unidade | Unidade | Sim | Relacionamento | Unidade 101 | nulo | Unidade à qual o veículo está vinculado. |
| dataInicio | Data | Sim | Simples | 2026-08-01 | data impossível | Data em que o veículo foi autorizado. |
| dataFim | Data | Não | Simples | 2027-03-01 | data anterior ao início | Data em que o vínculo deixou de valer. |

### 2.9. Registro de Acesso de Pessoa

Registro de Acesso de Pessoa representa cada entrada ou saída de uma pessoa no condomínio. Pode registrar acessos de moradores ou visitantes.

#### Atributos de Registro de Acesso de Pessoa

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 800, 921 | vazio, nulo | Código interno do registro. |
| tipoPessoa | Texto enumerado | Sim | Simples | MORADOR, VISITANTE | qualquer valor fora da lista | Indica se o acesso foi de morador ou visitante. |
| morador | Morador | Não | Relacionamento | Ana Souza | visitante preenchido junto sem regra clara | Deve ser preenchido quando `tipoPessoa` for MORADOR. |
| visitante | Visitante | Não | Relacionamento | Carlos Mendes | morador preenchido junto sem regra clara | Deve ser preenchido quando `tipoPessoa` for VISITANTE. |
| unidadeDestino | Unidade | Não | Relacionamento | Unidade 101 | unidade inexistente | Obrigatório para visitantes; opcional para moradores. |
| tipoMovimento | Texto enumerado | Sim | Simples | ENTRADA, SAIDA | qualquer valor fora da lista | Indica se o registro é de entrada ou saída. |
| dataHora | Data e hora | Sim | Simples | 2026-08-13 14:35:00 | data impossível | Momento do acesso. |
| formaIdentificacao | Texto enumerado | Sim | Simples | DOCUMENTO, RECONHECIMENTO_FACIAL, INTERFONE, MANUAL | qualquer valor fora da lista | Forma de identificação usada pela portaria. |
| autorizadoPorMorador | Morador | Não | Relacionamento | Ana Souza | morador inativo | Morador que autorizou a entrada do visitante, quando aplicável. |
| observacoes | Texto | Não | Simples | Entrega autorizada pela unidade 203 | texto ofensivo ou inconsistente | Observações da portaria. |

### 2.10. Registro de Acesso de Veículo

Registro de Acesso de Veículo representa cada entrada ou saída de veículo no condomínio. Pode registrar veículos de moradores ou visitantes.

#### Atributos de Registro de Acesso de Veículo

| Atributo / Dado | Tipo conceitual | Obrigatório | Característica | Ex. aceitos | Ex. não aceitos | Comentários |
| :--- | :---: | :---: | :---: | :--- | :--- | :--- |
| código | Código | Sim | Simples | 1, 900, 1050 | vazio, nulo | Código interno do registro. |
| veículo | Veículo | Sim | Relacionamento | ABC1D23 | veículo inexistente | Veículo que entrou ou saiu. |
| tipoResponsavel | Texto enumerado | Sim | Simples | MORADOR, VISITANTE | qualquer valor fora da lista | Indica se o veículo está associado a morador ou visitante. |
| moradorResponsavel | Morador | Não | Relacionamento | Ana Souza | visitante preenchido junto sem regra clara | Deve ser preenchido para veículo de morador. |
| visitanteResponsavel | Visitante | Não | Relacionamento | Carlos Mendes | morador preenchido junto sem regra clara | Deve ser preenchido para veículo de visitante. |
| unidadeDestino | Unidade | Não | Relacionamento | Unidade 101 | unidade inexistente | Obrigatório quando o veículo de visitante acessa uma unidade. |
| tipoMovimento | Texto enumerado | Sim | Simples | ENTRADA, SAIDA | qualquer valor fora da lista | Indica se o registro é de entrada ou saída. |
| dataHora | Data e hora | Sim | Simples | 2026-08-13 14:45:00 | data impossível | Momento do acesso do veículo. |
| observacoes | Texto | Não | Simples | Veículo entrou para manutenção | texto ofensivo ou inconsistente | Observações da portaria. |

## 3. Tipos especiais, compostos ou específicos

### 3.1. Documento de identificação

Documento de identificação é uma estrutura composta por tipo e valor. Ele permite representar diferentes documentos pessoais ou empresariais sem limitar o cadastro a apenas CPF.

| Atributo / Dado | Tipo conceitual | Obrigatório | Comentários |
| :--- | :--- | :---: | :--- |
| tipo | Texto enumerado | Sim | Valores previstos: CPF, CNPJ, RG, CNH ou PASSAPORTE. |
| valor | Texto | Sim | Valor do documento. Deve respeitar a regra do tipo informado quando houver validação disponível. |

### 3.2. Padrões de atributos especiais

| Código | Significado | Uso no dicionário |
| :---: | :--- | :--- |
| C | Composto | Usado quando um atributo possui partes internas. |
| M | Multivalorado | Usado quando um atributo pode ter vários valores. |
| MC | Multivalorado e composto | Usado quando há uma lista de estruturas compostas. |

## 4. Desdobramento físico ou tecnológico

### 4.1. Desdobramento para banco relacional

O modelo relacional separa as entidades principais em tabelas e representa os vínculos entre pessoas, unidades e veículos por meio de tabelas associativas. Os registros de entrada e saída são mantidos como tabelas históricas, pois representam eventos ocorridos na portaria.

### Tabela `UNIDADES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_unidade | integer | Sim | PK | Código único da unidade. |
| numero | varchar(20) | Sim | UK | Número ou código do apartamento. |
| andar | integer | Não |  | Andar da unidade. |
| situacao | varchar(20) | Sim |  | Valores: OCUPADA, VAGA, EM_REFORMA, INATIVA. |
| observacoes | varchar(500) | Não |  | Observações administrativas. |

### Tabela `CONDOMINOS`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_condomino | integer | Sim | PK | Código único do condômino. |
| nome | varchar(150) | Sim |  | Nome completo ou razão social. |
| documento_tipo | varchar(20) | Sim |  | Valores: CPF, CNPJ ou PASSAPORTE. |
| documento_valor | varchar(30) | Sim | UK | Valor do documento. |
| email | varchar(150) | Não |  | E-mail de contato. |
| telefone | varchar(30) | Não |  | Telefone principal. |
| tipo_responsavel | varchar(20) | Sim |  | Valores: PROPRIETARIO, LOCADOR. |
| ativo | boolean | Sim |  | Indica se o cadastro está ativo. |

### Tabela `MORADORES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_morador | integer | Sim | PK | Código único do morador. |
| nome | varchar(150) | Sim |  | Nome completo do morador. |
| documento_tipo | varchar(20) | Sim |  | Valores: CPF, RG, CNH ou PASSAPORTE. |
| documento_valor | varchar(30) | Sim | UK | Valor do documento. |
| data_nascimento | date | Não |  | Data de nascimento. |
| telefone | varchar(30) | Não |  | Telefone de contato. |
| foto_reconhecimento_facial | varchar(500) | Não |  | Caminho ou URL da foto usada para reconhecimento facial. |
| ativo | boolean | Sim |  | Indica se o morador está ativo. |

### Tabela `VISITANTES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_visitante | integer | Sim | PK | Código único do visitante. |
| nome | varchar(150) | Sim |  | Nome completo do visitante. |
| documento_tipo | varchar(20) | Não |  | Valores: CPF, RG, CNH ou PASSAPORTE. |
| documento_valor | varchar(30) | Não |  | Valor do documento. |
| telefone | varchar(30) | Não |  | Telefone de contato. |
| observacoes | varchar(500) | Não |  | Observações para apoio à portaria. |

### Tabela `VEICULOS`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_veiculo | integer | Sim | PK | Código único do veículo. |
| placa | varchar(10) | Sim | UK | Placa do veículo. |
| marca | varchar(60) | Não |  | Marca do veículo. |
| modelo | varchar(80) | Não |  | Modelo do veículo. |
| cor | varchar(40) | Não |  | Cor predominante. |
| tipo_veiculo | varchar(20) | Sim |  | Valores: AUTOMOVEL, MOTOCICLETA, BICICLETA. Triciclos devem ser cadastrados como MOTOCICLETA. |
| tipo_vinculo | varchar(20) | Sim |  | Valores: MORADOR, VISITANTE. |
| ativo | boolean | Sim |  | Indica se o veículo está ativo ou autorizado. |

### Tabela `CONDOMINOS_UNIDADES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_condomino_unidade | integer | Sim | PK | Código único do vínculo. |
| id_condomino | integer | Sim | FK | Referência para `CONDOMINOS`. |
| id_unidade | integer | Sim | FK | Referência para `UNIDADES`. |
| data_inicio | date | Sim |  | Início do vínculo. |
| data_fim | date | Não |  | Fim do vínculo, quando houver. |
| principal | boolean | Sim |  | Indica se é o principal responsável pela unidade. |

### Tabela `MORADORES_UNIDADES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_morador_unidade | integer | Sim | PK | Código único do vínculo. |
| id_morador | integer | Sim | FK | Referência para `MORADORES`. |
| id_unidade | integer | Sim | FK | Referência para `UNIDADES`. |
| data_entrada_moradia | date | Sim |  | Data em que o morador passou a residir na unidade. |
| data_saida_moradia | date | Não |  | Data em que deixou de residir na unidade. |
| tipo_morador | varchar(30) | Sim |  | Valores: PROPRIETARIO_RESIDENTE, INQUILINO, FAMILIAR, DEPENDENTE, OUTRO. |

### Tabela `VEICULOS_MORADORES`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_veiculo_morador | integer | Sim | PK | Código único do vínculo. |
| id_veiculo | integer | Sim | FK | Referência para `VEICULOS`. |
| id_morador | integer | Sim | FK | Referência para `MORADORES`. |
| id_unidade | integer | Sim | FK | Referência para `UNIDADES`. |
| data_inicio | date | Sim |  | Data inicial da autorização do veículo. |
| data_fim | date | Não |  | Data final da autorização, quando houver. |

### Tabela `REGISTROS_ACESSO_PESSOAS`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_registro_acesso_pessoa | integer | Sim | PK | Código único do registro. |
| tipo_pessoa | varchar(20) | Sim |  | Valores: MORADOR, VISITANTE. |
| id_morador | integer | Não | FK | Referência para `MORADORES`, quando o acesso for de morador. |
| id_visitante | integer | Não | FK | Referência para `VISITANTES`, quando o acesso for de visitante. |
| id_unidade_destino | integer | Não | FK | Referência para `UNIDADES`, principalmente em visitas. |
| tipo_movimento | varchar(10) | Sim |  | Valores: ENTRADA, SAIDA. |
| data_hora | timestamp | Sim |  | Data e hora do movimento. |
| forma_identificacao | varchar(30) | Sim |  | Valores: DOCUMENTO, RECONHECIMENTO_FACIAL, INTERFONE, MANUAL. |
| id_autorizado_por_morador | integer | Não | FK | Morador que autorizou o acesso, quando aplicável. |
| observacoes | varchar(500) | Não |  | Observações da portaria. |

### Tabela `REGISTROS_ACESSO_VEICULOS`

| Campo | Tipo físico | Obrigatório | Chave | Observação |
| :--- | :---: | :---: | :---: | :--- |
| id_registro_acesso_veiculo | integer | Sim | PK | Código único do registro. |
| id_veiculo | integer | Sim | FK | Referência para `VEICULOS`. |
| tipo_responsavel | varchar(20) | Sim |  | Valores: MORADOR, VISITANTE. |
| id_morador_responsavel | integer | Não | FK | Referência para `MORADORES`, quando veículo de morador. |
| id_visitante_responsavel | integer | Não | FK | Referência para `VISITANTES`, quando veículo de visitante. |
| id_unidade_destino | integer | Não | FK | Referência para `UNIDADES`, principalmente em visitas. |
| tipo_movimento | varchar(10) | Sim |  | Valores: ENTRADA, SAIDA. |
| data_hora | timestamp | Sim |  | Data e hora do movimento. |
| observacoes | varchar(500) | Não |  | Observações da portaria. |

### 4.2. Desdobramento para banco não relacional orientado a documentos: MongoDB

> Caso você esteja estudando bancos não relacionais, acrescentei esta seção para você poder visualizar documentos de exemplo e simulações de documentos reais.

Em um banco orientado a documentos, como o MongoDB, a modelagem pode agrupar dados que normalmente são consultados juntos. Nesta simulação, a coleção `unidades` concentra informações do apartamento, seus condôminos responsáveis e moradores ativos. Já visitantes, veículos e registros de acesso ficam em coleções próprias, pois podem ser consultados de forma independente pela portaria.

Esta é apenas uma possibilidade de modelagem documental. Em bancos não relacionais, a decisão entre embutir e referenciar dados depende do padrão de consulta, do volume esperado, da frequência de atualização e da necessidade de manter histórico.

#### Coleções sugeridas

| Coleção | Finalidade | Estratégia documental |
| :--- | :--- | :--- |
| `unidades` | Armazenar apartamentos, responsáveis e moradores ativos | Embute dados resumidos de condôminos e moradores. |
| `visitantes` | Armazenar visitantes cadastrados | Mantém visitantes em documentos próprios. |
| `veiculos` | Armazenar veículos de moradores e visitantes | Mantém veículos em documentos próprios, com referência ao responsável quando existir. |
| `registros_acesso_pessoas` | Registrar entradas e saídas de pessoas | Armazena eventos históricos de acesso. |
| `registros_acesso_veiculos` | Registrar entradas e saídas de veículos | Armazena eventos históricos de acesso de veículos. |

#### Exemplo de documento da coleção `unidades`

```json
{
  "_id": "unidade_101",
  "codigo": 101,
  "numero": "101",
  "andar": 1,
  "situacao": "OCUPADA",
  "observacoes": "Unidade sem pendências administrativas",
  "condominos": [
    {
      "codigo": 15,
      "nome": "Maria Oliveira",
      "documento": {
        "tipo": "CPF",
        "valor": "12345678901"
      },
      "email": "maria.oliveira@example.com",
      "telefone": "19999990000",
      "tipoResponsavel": "PROPRIETARIO",
      "principal": true,
      "dataInicio": "2024-03-01"
    }
  ],
  "moradores": [
    {
      "codigo": 41,
      "nome": "Ana Oliveira",
      "documento": {
        "tipo": "CPF",
        "valor": "98765432100"
      },
      "telefone": "19988887777",
      "tipoMorador": "FAMILIAR",
      "dataEntradaMoradia": "2024-03-01",
      "fotoReconhecimentoFacial": "/fotos/moradores/41.jpg",
      "ativo": true
    }
  ],
  "veiculosMoradores": [
    {
      "codigo": 30,
      "placa": "ABC1D23",
      "marca": "Toyota",
      "modelo": "Corolla",
      "cor": "Prata",
      "tipoVeiculo": "AUTOMOVEL",
      "idMorador": 41,
      "dataInicio": "2024-03-01",
      "ativo": true
    }
  ]
}
```

Neste documento, os condôminos, moradores e veículos de moradores aparecem embutidos porque normalmente a portaria e a administração consultam esses dados a partir da unidade.

#### Exemplo de documento da coleção `visitantes`

```json
{
  "_id": "visitante_200",
  "codigo": 200,
  "nome": "Carlos Mendes",
  "documento": {
    "tipo": "RG",
    "valor": "12345678"
  },
  "telefone": "19977776666",
  "observacoes": "Visitante recorrente da unidade 101"
}
```

Visitantes ficam em coleção própria porque uma mesma pessoa pode visitar o condomínio mais de uma vez e pode aparecer em muitos registros de acesso.

#### Exemplo de documento da coleção `veiculos`

```json
{
  "_id": "veiculo_80",
  "codigo": 80,
  "placa": "XYZ9A88",
  "marca": "Honda",
  "modelo": "CG 160",
  "cor": "Vermelha",
  "tipoVeiculo": "MOTOCICLETA",
  "tipoVinculo": "VISITANTE",
  "visitante": {
    "codigo": 200,
    "nome": "Carlos Mendes"
  },
  "ativo": true
}
```

Veículos podem ser armazenados separadamente para facilitar buscas por placa, principalmente em rotinas de entrada e saída.

#### Exemplo de documento da coleção `registros_acesso_pessoas`

```json
{
  "_id": "acesso_pessoa_1000",
  "codigo": 1000,
  "tipoPessoa": "VISITANTE",
  "visitante": {
    "codigo": 200,
    "nome": "Carlos Mendes",
    "documento": {
      "tipo": "RG",
      "valor": "12345678"
    }
  },
  "unidadeDestino": {
    "codigo": 101,
    "numero": "101"
  },
  "tipoMovimento": "ENTRADA",
  "dataHora": "2026-08-13T14:35:00-03:00",
  "formaIdentificacao": "DOCUMENTO",
  "autorizadoPorMorador": {
    "codigo": 41,
    "nome": "Ana Oliveira"
  },
  "observacoes": "Entrada autorizada pelo interfone"
}
```

Registros de acesso são documentos históricos. Por isso, é aceitável armazenar uma cópia resumida dos dados da pessoa e da unidade no momento do acesso. Assim, mesmo que o cadastro do visitante ou morador mude depois, o histórico preserva o contexto daquele evento.

#### Exemplo de documento da coleção `registros_acesso_veiculos`

```json
{
  "_id": "acesso_veiculo_500",
  "codigo": 500,
  "veiculo": {
    "codigo": 80,
    "placa": "XYZ9A88",
    "tipoVeiculo": "MOTOCICLETA",
    "marca": "Honda",
    "modelo": "CG 160",
    "cor": "Vermelha"
  },
  "tipoResponsavel": "VISITANTE",
  "visitanteResponsavel": {
    "codigo": 200,
    "nome": "Carlos Mendes"
  },
  "unidadeDestino": {
    "codigo": 101,
    "numero": "101"
  },
  "tipoMovimento": "ENTRADA",
  "dataHora": "2026-08-13T14:40:00-03:00",
  "observacoes": "Veículo autorizado para vaga de visitante"
}
```

#### Observações sobre a modelagem documental

- Dados consultados juntos podem ser embutidos no mesmo documento.
- Dados com histórico próprio, como registros de acesso, devem ficar em documentos próprios.
- Informações copiadas para registros históricos podem gerar duplicação, mas ajudam a preservar o estado do evento no momento em que ele ocorreu.
- Campos como `documento`, `visitante`, `morador`, `unidadeDestino` e `veiculo` podem aparecer como estruturas compostas dentro dos documentos.
- Para consultas frequentes, seriam bons candidatos a índice: `unidades.numero`, `unidades.moradores.documento.valor`, `veiculos.placa`, `registros_acesso_pessoas.dataHora`, `registros_acesso_veiculos.dataHora` e `registros_acesso_pessoas.unidadeDestino.codigo`.

### 4.3. Justificativa do desdobramento relacional

O desdobramento relacional foi escolhido para esta primeira versão porque o domínio possui muitos relacionamentos formais entre pessoas, unidades e veículos. A separação em tabelas facilita o controle de integridade referencial e permite manter histórico de vínculos e registros de acesso.

As tabelas associativas `CONDOMINOS_UNIDADES`, `MORADORES_UNIDADES` e `VEICULOS_MORADORES` evitam duplicação de dados e permitem representar mudanças ao longo do tempo, como troca de moradores, alteração de responsável pela unidade ou substituição de veículo.

Os registros de entrada e saída foram separados em `REGISTROS_ACESSO_PESSOAS` e `REGISTROS_ACESSO_VEICULOS` porque representam eventos diferentes. Uma pessoa pode entrar sem veículo, um veículo pode ser registrado com seu responsável, e ambos os históricos podem ser consultados de forma independente.

## 5. Consultas, índices e observações de arquitetura

### 5.1. Consultas esperadas

| Consulta esperada | Campos envolvidos | Índices sugeridos | Observações |
| :--- | :--- | :--- | :--- |
| Buscar unidade pelo número | `UNIDADES.numero` | `idx_unidades_numero` | Consulta frequente em rotinas de portaria e administração. |
| Buscar condôminos de uma unidade | `CONDOMINOS_UNIDADES.id_unidade` | `idx_condominos_unidades_unidade` | Permite identificar responsáveis financeiros. |
| Buscar moradores ativos de uma unidade | `MORADORES_UNIDADES.id_unidade`, `data_saida_moradia` | `idx_moradores_unidades_unidade` | Deve considerar vínculos sem data de saída. |
| Buscar morador por documento | `MORADORES.documento_tipo`, `MORADORES.documento_valor` | `idx_moradores_documento` | Útil para identificação na portaria. |
| Buscar visitante por documento | `VISITANTES.documento_tipo`, `VISITANTES.documento_valor` | `idx_visitantes_documento` | Útil para visitantes recorrentes. |
| Buscar veículo por placa | `VEICULOS.placa` | `idx_veiculos_placa` | Consulta crítica para controle de garagem. |
| Consultar acessos de pessoa por período | `REGISTROS_ACESSO_PESSOAS.data_hora` | `idx_acesso_pessoas_data_hora` | Útil para auditoria. |
| Consultar acessos de veículo por período | `REGISTROS_ACESSO_VEICULOS.data_hora` | `idx_acesso_veiculos_data_hora` | Útil para auditoria. |
| Consultar visitas destinadas a uma unidade | `REGISTROS_ACESSO_PESSOAS.id_unidade_destino`, `data_hora` | `idx_acesso_pessoas_unidade_data` | Ajuda a acompanhar visitas por apartamento. |

### 5.2. Regras e observações

- Uma unidade deve possuir número único.
- Um condômino ativo deve possuir documento válido.
- Um morador ativo deve estar vinculado a pelo menos uma unidade ativa.
- Um veículo de morador deve possuir vínculo ativo com morador e unidade.
- Um registro de acesso de pessoa deve referenciar morador ou visitante, conforme o valor de `tipo_pessoa`.
- Um registro de acesso de veículo deve referenciar morador ou visitante responsável, conforme o valor de `tipo_responsavel`.
- Visitantes podem ser cadastrados sem documento em uma versão simples, mas a regra pode ser endurecida pelo condomínio.
- A foto de reconhecimento facial pode ser armazenada como dado binário no banco ou como arquivo externo referenciado por caminho, URL ou identificador. Neste exemplo, foi adotada a estratégia de referência externa.
- Registros de acesso não devem ser removidos fisicamente em uso normal, pois compõem histórico de auditoria.
