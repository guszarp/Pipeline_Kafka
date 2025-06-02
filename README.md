ETL de Vendas e Dashboard de KPIs em Tempo Real
Este projeto apresenta um pipeline ETL completo — desde a geração de dados até a exibição de KPIs em tempo real — com foco em práticas modernas de engenharia de dados. Ele simula um fluxo de vendas utilizando armazenamento em nuvem, monitoramento via Kafka e visualização interativa. Neste documento, você encontrará detalhes sobre a arquitetura do sistema, os principais componentes envolvidos e instruções para executar o projeto localmente ou com Docker.

📚 Sumário
Visão Geral

Tecnologias e Bibliotecas

Arquitetura

Configuração do Ambiente

Fluxo de Dados

KPIs Monitorados

Visão Geral
A proposta do projeto é simular a rotina de geração de relatórios de vendas diários. Um arquivo CSV com dados simulados é criado e enviado a um bucket S3 da AWS, que atua como ponto de entrada para o pipeline. Um processo automático monitora esse bucket e, ao detectar novos arquivos, inicia o pipeline ETL que:

Extrai os dados do bucket S3;

Transforma os dados com limpeza e validação usando pandas e pydantic;

Carrega os dados em um banco de dados PostgreSQL.

Esses dados são então consumidos por uma aplicação Streamlit, que exibe indicadores de desempenho de vendas em tempo real.

Tecnologias e Bibliotecas
Infraestrutura
AWS S3 – Armazena os arquivos CSV de vendas, servindo como fonte do ETL

PostgreSQL – Banco de dados relacional onde os dados transformados são armazenados

Docker – Facilita a criação e execução de ambientes isolados

Kafka – Responsável por acionar e monitorar o pipeline ETL por meio de mensagens

Ferramentas de Apoio
DBeaver – Interface de gerenciamento e consulta ao banco de dados

Principais Bibliotecas Python
pandas – Manipulação e transformação de dados

boto3 – SDK para comunicação com serviços AWS (como S3)

Faker – Geração de dados de vendas fictícios

pydantic – Validação e integridade dos dados em cada etapa do pipeline

sqlalchemy – ORM para integração com o PostgreSQL

streamlit – Framework para criação de dashboards interativos em tempo real

confluent_kafka – Biblioteca de integração com Kafka

Arquitetura
A arquitetura é modular e bem definida, permitindo controle total do fluxo de dados:

Geração de Dados: O script csv_generator.py cria arquivos com dados de vendas simulados.

Armazenamento e Detecção: Os arquivos são enviados para o S3 e monitorados por Kafka.

Pipeline ETL:

Extração: Download do CSV via boto3.

Transformação: Limpeza e validação com pandas e pydantic.

Carga: Inserção no PostgreSQL usando sqlalchemy.

Dashboard: Aplicação em Streamlit que consome os dados do PostgreSQL e apresenta KPIs atualizados em tempo real.

Configuração do Ambiente
Requisitos
Docker

AWS CLI configurado

PostgreSQL

Kafka

Passos para Execução
Clone o repositório:

bash
Copiar
Editar
git clone git@github.com/caio-moliveira/sales-pipeline-project.git
cd sales-pipeline-project
Crie o arquivo .env com as configurações necessárias para PostgreSQL, AWS e Kafka.

Inicie os serviços com Docker:

bash
Copiar
Editar
docker-compose up --build
Variáveis de Ambiente
As seguintes variáveis devem ser definidas no arquivo .env na raiz do projeto:

AWS
env
Copiar
Editar
AWS_ACCESS_KEY_ID=sua_chave_de_acesso
AWS_SECRET_ACCESS_KEY=sua_chave_secreta
AWS_REGION=sua_regiao (ex: us-east-1)
BUCKET_NAME=nome_do_bucket
PostgreSQL
env
Copiar
Editar
POSTGRES_USER=usuario
POSTGRES_PASSWORD=senha
POSTGRES_HOST=host
POSTGRES_DB=nome_do_banco
Kafka
env
Copiar
Editar
BOOTSTRAP_SERVERS=host_do_kafka
SASL_USERNAME=usuario_kafka
SASL_PASSWORD=senha_kafka
CLIENT_ID=id_cliente_kafka
⚠️ Importante: adicione o .env no .gitignore para evitar o versionamento de dados sensíveis.

Fluxo de Dados
Geração de Dados

Com Faker, um arquivo CSV com vendas fictícias é gerado e armazenado no S3.

Extração

O script detecta o novo arquivo e o baixa utilizando boto3.

Transformação

pandas organiza os dados e pydantic garante a integridade dos registros.

Carga

Os dados validados são inseridos no PostgreSQL via sqlalchemy.

Visualização

A aplicação em Streamlit se conecta ao banco e exibe os KPIs em tempo real.

KPIs Monitorados
O dashboard mostra os seguintes indicadores de desempenho:

Vendas Totais – Soma do total de vendas no dia

Valor Médio por Transação – Valor médio por venda

Produtos Mais Vendidos – Lista dos produtos com maior volume de vendas

Vendas por Categoria – Distribuição das vendas por categoria de produto

Tendência de Vendas – Gráfico em tempo real da evolução das vendas
