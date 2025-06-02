# ETL de Vendas e Dashboard de KPIs em Tempo Real

Este projeto apresenta um pipeline ETL completo — desde a geração de dados até a exibição de KPIs em tempo real — com foco em práticas modernas de engenharia de dados. Ele simula um fluxo de vendas utilizando armazenamento em nuvem, monitoramento via Kafka e visualização interativa. Neste documento, você encontrará detalhes sobre a arquitetura do sistema, os principais componentes envolvidos e instruções para executar o projeto localmente ou com Docker.
## Índice

- [Visão Geral do Projeto](#Visão Geral do Projeto)
- [Tecnologias e Bibliotecas Utilizadas](#Tecnologias e Bibliotecas Utilizadas)
- [Arquitetura](#Arquitetura)
- [Instruções de Configuração](#Instruções de Configuração)
- [Fluxo de Dados](#Fluxo de Dados)
- [Indicadores-Chave de Performance (KPIs)](#Indicadores-Chave de Performance (KPIs))

---

## Visão Geral do Projeto

A proposta do projeto é simular a rotina de geração de relatórios de vendas diários. Um arquivo CSV com dados simulados é criado e enviado a um bucket S3 da AWS, que atua como ponto de entrada para o pipeline. Um processo automático monitora esse bucket e, ao detectar novos arquivos, inicia o pipeline ETL que:
1. **Extrair**: Recuperar o arquivo do bucket S3.
2. **Transformar**: Limpar e validar os dados usando `pandas` e `pydantic` garantindo integridade.
3. **Carregar**: Inserir os dados em um banco de dados PostgreSQL.

Os dados são então visualizados por meio de uma aplicação web feita com Streamlit, que acessa o banco PostgreSQL e exibe os principais KPIs de vendas em tempo real.

## Tecnologias e Bibliotecas Utilizadas

### Infraestrutura
- **AWS S3**: Armazenamento dos arquivos de vendas, atuando como fonte de dados do ETL.
- **PostgreSQL**: Armazena os dados transformados, permitindo consultas em tempo real.
- **Docker**: Containerização dos serviços para facilitar a implantação e gerenciamento.
- **Kafka**: Sistema de filas de mensagens para monitoramento e disparo do ETL.

### Ferramentas de Desenvolvimento e Análise
- **DBeaver**: Gerenciamento e consulta de banco de dados.
  
### Principais Bibliotecas Python

- **pandas**: Manipulação e transformação de dados.
- **boto3**: SDK da AWS para Python, usado para interagir com o S3.
- **Faker**: Simulação de dados de vendas.
- **pydantic**: DValidação de dados, garantindo qualidade em cada etapa do pipeline.
- **sqlalchemy**: ORM para inserção dos dados no PostgreSQL.
- **streamlit**: Dashboard de KPIs em tempo real.
- **confluent_kafka**: Interface com Kafka, responsável pela execução reativa do ETL.

## Arquitetura

A arquitetura é bem definida, permitindo controle total do fluxo de dados:


Arquitetura![image](https://github.com/user-attachments/assets/51d132c6-4ae1-4caa-ac4c-153eaef6b3db)


1. **Geração de Dados**: `csv_generator.py` cria arquivos CSV simulando vendas diárias.
2. **Armazenamento e Monitoramento (S3 + Kafka)**: Os arquivos são armazenados em um bucket S3 e monitorados por um tópico Kafka que aciona o ETL quando um novo arquivo é detectado.
3. **Pipeline ETL**:
   - **Extração**: Baixa o CSV do S3.
   - **Transformação**: Valida e limpa os dados com `pydantic`.
   - **Carrega**: Insere os dados no PostgreSQL.
4. **Visualização**: Aplicação em Streamlit que consome dados do PostgreSQL para mostrar KPIs em tempo real.

## Instruções de Configuração

### Pré-requisitos

- Docker
- AWS CLI configurado com acesso ao S3
- PostgreSQL
- Kafka
  
### Executando o Projeto

1. **Clone the repository**:
   ```bash
   git clone git@github.com/caio-moliveira/sales-pipeline-project.git
   cd sales-pipeline-project
   ```
2. **Crie e configure o arquivo** `.env` com as credenciais do PostgreSQL, AWS e Kafka.


3. **Execute os serviços Docker:**
  
  ```bash
  docker-compose up --build
  ```

4. **Variáveis de Ambiente**

Este projeto depende de variáveis de ambiente específicas para funcionar corretamente. Elas devem ser definidas no arquivo `.env` na raiz do projeto.

### Configuração da AWS

- **`AWS_ACCESS_KEY_ID`**: Chave de acesso da AWS.
- **`AWS_SECRET_ACCESS_KEY`**: Chave secreta da AWS.
- **`AWS_REGION`**: Região da AWS onde os serviços estão hospedados (ex: `us-east-1`).
- **`BUCKET_NAME`**: Nome do bucket S3.

### Configuração do PostgreSQL

- **`POSTGRES_USER`**: Usuário do banco.
- **`POSTGRES_PASSWORD`**: Senha do banco.
- **`POSTGRES_HOST`**: ndereço do host PostgreSQL.
- **`POSTGRES_DB`**: Nome do banco de dados.

### Configuração do Kafka
- **`BOOTSTRAP_SERVERS`**: Endereço(s) do broker Kafka (ex: `broker1:9092,broker2:9092`).
- **`SASL_USERNAME`**: Nome de usuário do Kafka.
- **`SASL_PASSWORD`**: Senha do Kafka.
- **`CLIENT_ID`**: Identificador único do cliente Kafka.

#### Passos para Configuração
1. Crie um arquivo `.env` na raiz do projeto.
2. Copie o exemplo abaixo e substitua os valores pelos seus dados reais.
3. Certifique-se de adicionar `.env` no `.gitignore` para evitar o versionamento de informações sensíveis.

### Exemplo de `.env`
```env
AWS_ACCESS_KEY_ID=sua_chave_aws
AWS_SECRET_ACCESS_KEY=sua_chave_secreta_aws
AWS_REGION=sua_regiao
BUCKET_NAME=nome_do_bucket

POSTGRES_USER=usuario_postgres
POSTGRES_PASSWORD=senha_postgres
POSTGRES_HOST=host_postgres
POSTGRES_DB=banco_postgres

BOOTSTRAP_SERVERS=seu_kafka_broker
SASL_USERNAME=usuario_kafka
SASL_PASSWORD=senha_kafka
CLIENT_ID=cliente_kafka
```

 ## Fluxo de Dados

1. **Geração de Dados**: 
   - Com  `Faker` , um arquivo CSV com vendas fictícias é gerado e armazenado no S3.
2. **Extração**:
   - O script detecta o novo arquivo e o baixa utilizando `boto3`.
3. **Transformação**:
   - `pandas` organiza os dados e `pydantic` garante a integridade dos registros.
4. **Carregar**:
   - Os dados validados são inseridos no PostgreSQL via `sqlalchemy`.
5. **Visualização**:
   - A aplicação em Streamlit se conecta ao banco e exibe os KPIs em tempo real..

## KPIs Monitorados

O dashboard mostra os seguintes indicadores de desempenho:

- **Vendas Totais**: Soma do total de vendas no dia
- **Valor Médio por Transação**: Valor médio por venda
- **Produtos Mais Vendidos**: Lista dos produtos com maior volume de vendas.
- **Vendas por Categoria**: Distribuição das vendas por categoria de produto.
- **endência de Vendas**: Gráfico em tempo real da evolução das vendas.
