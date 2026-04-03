# E-Commerce Data Engineering — Rocket Lab 2026

O objetivo é estruturar os dados de um grande e-commerce utilizando a arquitetura
Medalhão no Databricks, implementando todo o processo de ETL desde a ingestão
dos dados brutos até a geração de Data Marts analíticos.

---

## Dataset

- **Fonte:** [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (Kaggle)
- **Cotação do Dólar:** API do Banco Central do Brasil (PTAX)

---

## Arquitetura

O projeto segue a **Arquitetura Medalhão** com as seguintes camadas:

Landing Zone → Bronze → Silver → Gold

| Camada | Descrição |
|--------|-----------|
| **Landing** | Arquivos CSV brutos carregados no Volume do Databricks |
| **Bronze** | Dados ingeridos no formato Delta com timestamp de ingestão |
| **Silver** | Dados limpos, padronizados e tipados em português |
| **Gold** | Data Marts analíticos prontos para consumo de BI |

---

## Estrutura do Repositório
| Arquivo | Descrição |
|---------|-----------|
| `bronze.ipynb` | Ingestão dos CSVs e API do Banco Central |
| `silver.ipynb` | Transformações, limpeza e padronização |
| `gold.ipynb` | Data Marts analíticos e rankings |
| `job_workflow.yaml` | Exportação do Workflow do Databricks |
| `print_execucao_job.webp` | Print da execução com sucesso do pipeline |

## 🥉 Camada Bronze

Notebook responsável por:
- Criar o schema `olist.bronze`
- Ingerir os 9 arquivos CSV do dataset Olist como Delta Tables
- Adicionar a coluna `timestamp_ingestion` em cada tabela
- Extrair a cotação do dólar via API do Banco Central (PTAX)

**Tabelas geradas:**
- `bronze.tb_customers`
- `bronze.tb_geolocalizacao`
- `bronze.tb_order_items`
- `bronze.tb_order_payments`
- `bronze.tb_order_reviews`
- `bronze.tb_orders`
- `bronze.tb_products`
- `bronze.tb_sellers`
- `bronze.tb_product_category_name_translation`
- `bronze.tb_cotacao_dolar`

---

## 🥈 Camada Silver

Notebook responsável por:
- Criar o schema `olist.silver`
- Aplicar limpeza, padronização e tipagem dos dados
- Traduzir colunas e valores de inglês para português
- Aplicar deduplicação via Window Functions
- Criar colunas derivadas de tempo de entrega
- Preencher o calendário de cotação do dólar para finais de semana
- Aplicar OPTIMIZE e ZORDER nas tabelas fato para performance

**Tabelas geradas:**
- `silver.dim_consumidores`
- `silver.dim_produtos`
- `silver.dim_vendedores`
- `silver.dim_categoria_produtos_traducao`
- `silver.dim_cotacao_dolar`
- `silver.fat_pedidos`
- `silver.fat_itens_pedidos`
- `silver.fat_pagamentos_pedidos`
- `silver.fat_avaliacoes_pedidos`
- `silver.fat_pedido_total`

---

## 🥇 Camada Gold

Notebook responsável por criar os Data Marts analíticos:

### 1º Projeto — Visão Comercial e Volume de Produtos
- **`gold.fat_vendas_comercial`** — receitas agregadas por Ano, Mês e Categoria
- **Top 5 Produtos Mais Vendidos** — exibido via `display()`
- **Top 5 Produtos Menos Vendidos** — exibido via `display()`

### 2º Projeto — Satisfação de Clientes e Qualidade de Parceiros
- **`gold.fat_avaliacoes_clientes`** — avaliações agregadas por Categoria, Vendedor e Estado
- **Produto mais e menos bem avaliado** — exibido via `display()`
- **Vendedor mais e menos bem avaliado** — exibido via `display()`

---

## Orquestração

O pipeline é orquestrado via **Databricks Workflows** com:
- 3 tasks sequenciais: `Bronze → Silver → Gold`
- Agendamento diário às **13:00 (America/Recife)**
- Retry automático em caso de falha

---

## Tecnologias

- **Databricks** — plataforma de processamento de dados
- **Apache Spark / PySpark** — processamento distribuído
- **Delta Lake** — formato de armazenamento com ACID e time travel
- **Python** — linguagem principal dos notebooks
- **SQL** — consultas e otimizações
