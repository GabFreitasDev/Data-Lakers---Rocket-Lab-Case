# Case Rocket Lab - Data-Lakers
Este repositório contém a solução desenvolvida para o Case Rocket Lab 2025, proposto pela Viságio, aplicada ao contexto da V-Credit, uma instituição financeira que enfrenta desafios operacionais envolvendo múltiplos canais de atendimento, custos elevados e baixa satisfação dos clientes. A arquitetura do projeto foi construída no Databricks, seguindo o padrão Medalhão (Landing → Bronze → Silver → Gold), utilizando pipelines em PySpark/SQL, processamento escalável, modelagem dimensional e criação de uma camada analítica robusta para geração de insights.

Para garantir confiabilidade e automação de ponta a ponta, todo o pipeline foi orquestrado por meio de Databricks Jobs, com tasks sequenciais representando cada etapa do fluxo de dados. Além disso, os insights obtidos na camada Gold foram posteriormente organizados e apresentados em um dashboard interativo no Power BI, permitindo análises visuais sobre efetividade dos canais, custos, satisfação do cliente, comportamento por faixa etária e eficiência operacional.

##  Arquitetura de Dados
Landing → Bronze → Silver → Gold 

### Primeira Camada: Land → Bronze
Notebook: Notebook_land_to_bronze.py

#### Principais ações:
- Leitura dos arquivos CSV da Landing
- Validação do conteúdo
- Escrita em Delta nas tabelas bronze

#### Arquivos ingeridos:
- Canais
- Motivos
- Chamados
- Chamados_hora
- Clientes
- Atendentes
- Cstos
- Pesquisa de satisfação


### Segunda Camada: Bronze → Silver
Notebook: Notebook_bronze_to_silver.py

#### Transformações realizadas:
- Tipagem correta das colunas
- Deduplicação e limpeza dos dados
- Padronização de texto
- Correção de atributos
- Criação das estruturas dimensionais:
    - Clientes
    - Canais
    - Motivos
    - Atendentes
    - Tempo

### Terceira Camada: Silver → Gold
Notebook: Notebook_silver_to_gold.py

#### Tabelas Fato e Dimensão
- ft_chamados_atendimento
- dim_cliente
- dim_canal
- dim_motivo
- dim_atendente
- dim_tempo

#### Views analíticas para ingestão no dashboard:
- view_funil_gargalo
- view_efetividade_canal
- view_efetividade_motivo
- view_efetividade_motivo_canal
- view_motivo_faixa_etaria
- view_kpi_qualidade_cliente
- view_eficiencia_canal_faixa_etaria
- view_recorrencia_chamados_faixa_etaria
- view_recorrencia_com_fcr
- view_performance_por_demografia
- view_top_motivos_impacto
- view_comparativo_atendente_motivo
- view_fcr_por_canal
- view_custo_resolucao_canal
  
#### Indicadores Construídos:
- TME: tempo médio de espera
- TMA: tempo médio de atendimento
- Volume de chamados por canal e motivo
- Efetividade por canal e categoria
- FCR (First Contact Resolution): resolução no primeiro contato
- Nota média de satisfação
- Custo por chamado e por canal
- Recorrência e probabilidade de recontato

## Principais Insights Obtidos
- Canais automatizados (URA e Chatbot) apresentam menor taxe de efetividade
- Canais com atendimento humano, embora mais caros, aumentam a taxa de satisfação do cliente
- Clientes idosos possuem uma satisfação menor em relação à canais automatizados
- Motivos da categoria “cartão” concentram os maiores custos e chamados
- Canais com atendimento humano apresentam maior taxa resolução
- Motivos ligados a “fraude” e “bloqueio preventivo” têm o maior tempo médio de atendimento
- Faixas etárias mais jovens usam mais canais digitais/automatizados
- Motivos relacionados a renegociação de dívida concentram o maior tempo de espera
- Chamados originados de canais automatizados tendem a migrar para atendimento humano


## Como Reproduzir
### Criar catálogo e schemas
```sql
create catalog medalhao_case;
create schema medalhao_case.bronze;
create schema medalhao_case.silver;
create schema medalhao_case.gold;
```

### Criar o volume Landing
```sql
create volume default.landing;
```

### Subir os arquivos CSV para o volume
Caminho no Databricks:
Catalog → Volumes → default.landing → Upload

#### Arquivos necessários:
- canais.csv
- motivos.csv
- chamados.csv
- chamados_hora.csv
- clientes.csv
- atendentes.csv
- custos.csv
- pesquisa_satisfacao.csv

### Executar os notebooks
1) Land → Bronze <br>
   Realiza a ingestão e validação dos dados + criação das tabelas Bronze

3) Bronze → Silver <br>
    Realiza a limpeza, padronização e deduplicação dos dados + criação das dimensões

4) Silver → Gold <br>
    Realiza a modelagem dimensional: fato, dimensões e views analíticas




