Este repositório contém o código necessário para processar dados de pedidos e produtos. O arquivo principal é o processamento.py, que manipula dados de três arquivos CSV para gerar um arquivo de saída processado.

Estrutura do Projeto
Copiar
Editar
.
├── base_de_dados/
│   ├── order_products__prior.csv
│   ├── products.csv
│   ├── departments.csv
├── processamento.py
├── README.md
Descrição do arquivo processamento.py
O script processamento.py realiza as seguintes etapas de processamento:

1. Carregamento dos dados
O código começa lendo três arquivos CSV com dados sobre os pedidos, produtos e departamentos.

python
Copiar
Editar
import pandas as pd

prior_file = 'base_de_dados/order_products__prior.csv'
products_file = 'base_de_dados/products.csv'
departments_file = 'base_de_dados/departments.csv'

orders = pd.read_csv(prior_file, usecols=['order_id', 'product_id'])
products = pd.read_csv(products_file, usecols=['product_id', 'product_name', 'department_id'])
departments = pd.read_csv(departments_file, usecols=['department_id', 'department'])
Esses arquivos estão armazenados na pasta base_de_dados/, e o código lê as colunas necessárias para realizar o processamento: order_id, product_id, product_name, department_id e department.

2. Processamento e Junção dos Dados
Após carregar os dados, o código realiza uma junção (merge) entre as tabelas de pedidos (orders) e produtos (products), utilizando a coluna product_id.

python
Copiar
Editar
merged_data = pd.merge(orders, products, on='product_id', how='left')
Essa junção garante que cada produto de um pedido seja associado ao nome do produto correspondente.

3. Adicionando o Departamento
Em seguida, o script faz outra junção para adicionar a coluna de departamentos, associando cada produto ao seu departamento correspondente.

python
Copiar
Editar
merged_data = pd.merge(merged_data, departments, on='department_id', how='left')
4. Gerando o Arquivo Final
Após a junção dos dados, o script grava o arquivo final com as informações processadas.

python
Copiar
Editar
