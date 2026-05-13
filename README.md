# projeto-integrador-grupoB
Trabalho de conclusão do trabalho de projeto integrador - PECEPOLI USP 2026

## Projeto Integrador - Yelp Data Ingestion

## 1. Visão Geral do Projeto e Identificação do Grupo
Este repositório contém os scripts, notebooks e dashboards correspondentes ao desenvolvimento de um *pipeline* de dados fim-a-fim, projetado para o processamento de dados do Yelp. O trabalho foi desenvolvido para a disciplina eEDB-015/2026-1 - Projeto Integrador da Especialização em Engenharia de Dados e Big Data do PECE/EPUSP.

**Integrantes do Grupo B**:
* Ana Carolina Kubo
* Danilo de Souza Silva
* Jefferson da Silva Almeida
* Willian Camargo Aires Maranhão

## 2. Descrição do Problema e Objetivos
**Problema:** Há uma vasta quantidade de dados desestruturados do Yelp (usuários, *reviews* e estabelecimentos) que precisam ser organizados e cruzados para a extração de inteligência de negócios. É necessário criar um repositório central que facilite a análise do comportamento dos clientes, o desempenho do nicho de alimentação e, principalmente, auxilie na detecção de possíveis fraudes, como avaliações publicadas quando os estabelecimentos constam como fechados.

**Objetivos:**
* Desenvolver uma esteira de ingestão para consumir dados da API do Kaggle e armazená-los localmente.
* Estruturar, limpar e refinar as bases (focando em estabelecimentos de alimentação), tratando nulos, desambiguando nomes e corrigindo *strings*.
* Desenvolver tabelas agregadas que suportem *dashboards* táticos com métricas de fraude, *score* de localidade, comportamento de clientes, *marketing* de restaurantes e sazonalidade.

## 3. Resumo da Arquitetura e Principais Tecnologias
O projeto emprega uma **Arquitetura Medalhão** (Landing, Bronze, Silver, Gold) executada integralmente em um ambiente **Databricks**.
* **Landing Zone & Bronze:** Ingestão de arquivos JSON comprimidos via Kaggle API (`YELP_ING` / `YELP_BRONZE`), com gravação em tabelas em modo *append* para preservar o histórico.
* **Silver:** Notebooks dedicados a processar, filtrar (ex: somente estabelecimentos correlacionados a "Food") e higienizar os dados brutos.
* **Gold:** Modelagem analítica gerando indicadores compostos, modelos como "Rating Bayesiano" e tabelas agregadas para consumo do negócio.

**Tecnologias utilizadas:** Apache Spark (PySpark), Databricks (Notebooks e Dashboards), Python, SQL e formato Delta.

## 4. Organização das Pastas e Arquivos
O repositório consolida todos os artefatos necessários listados na estrutura do projeto, separados logicamente por camadas:

* **Camada Bronze (Ingestão)**:
  * `api_kaggle_Volume_VF`: Consumo de dados e criação dos volumes.
  * `Script_Ingestao`: Movimentação da *Landing Zone* para o *schema* Bronze.
* **Camada Silver (Refinamento e Qualidade)**:
  * `Criação da tabela base silver_business_v3`: Higienização e filtros de categorias de restaurantes.
  * `Criação da tabela base silver_review_v2`: Limpeza e controle de completude das avaliações.
  * `Criação da tabela base silver_user_v3`: Estruturação de dados de usuários qualificados.
* **Camada Gold (Regras de Negócio Analíticas)**:
  * `alertas_possiveis_fraudes_gold_v4`: Detecção de avaliações suspeitas via cruzamento de horários.
  * `analise_comportamento_clientes_gold_v5`: *Scoring* e agregados sobre os usuários.
  * `marketing_restaurantes_gold_v3`: Regras avançadas de *rating* bayesiano e oportunidades.
  * `reviews_localidade_gold_v1`: Agregações métricas por estado/cidade.
  * `sazonalidade_gold`: Insights sobre volume de reviews baseados no dia da semana.
* **Dashboards (Camada de Apresentação visual)**:
  * 

## 5. Instruções para Instalação, Configuração e Execução
1. **Configuração do Ambiente:**
   * Crie um *Workspace* do Databricks e certifique-se de ter os privilégios necessários para manipulação do *Catalog*.
   * Importe todos os *notebooks* deste repositório para o seu espaço de trabalho.
   * Configure os *schemas* destino (ex: `yelp_bronze`, `yelp_silver`, `yelp_gold` / `yelp_ing`).
2. **Execução do *Pipeline* (na ordem abaixo):**
   * Rode o notebook `api_kaggle_Volume_VF` para processar e gravar na *Landing Zone* os arquivos JSON do Kaggle.
   * Execute `Script_Ingestao` para transcrever os arquivos JSON em tabelas Delta (Camada Bronze).
   * Execute os três *notebooks* da Camada Silver na sequência desejada (Business, Review e User).
   * Execute os *notebooks* da Camada Gold para preparar as lógicas finais e popular o repositório analítico.
3. **Configuração dos Dashboards:**
   * Importe os arquivos `.lvdash.json` e `.lvdash` na aba de interface do *Databricks Dashboards*, certificando-se de associá-los às tabelas recém-criadas na Camada Gold.

## 6. Orientações para Acesso aos Dados e Uso do Sistema
O consumo final de informações pelo usuário de negócios deve ser realizado primariamente de modo visual por meio dos quatro **Dashboards** configurados no sistema. Esses painéis permitem filtragem de dados e exploração interativa de métricas temporais e comportamentais (como sazonalidade e potenciais fraudes).
Para os cientistas ou engenheiros de dados que requeiram acesso analítico ad-hoc (SQL), as tabelas limpas estão catalogadas no ambiente Databricks e podem ser consultadas referenciando `<catalog>.<schema_gold>.<nome_da_tabela>`.

##**Tabelas do Schema yelp_gold**
1. Tabelas de Análise de Negócio e Marketing
workspace.yelp_gold.marketing - Análise de oportunidades de marketing com rating Bayesiano dos restaurantes
workspace.yelp_gold.gold_business_reviews_integrado - Dados integrados de estabelecimentos e reviews com informações de sazonalidade
workspace.yelp_gold.gold_top10_estabelecimentos - Top 10 restaurantes por segmento, estado e ano
2. Tabelas de Comportamento de Usuários
workspace.yelp_gold.user_behavior_global - Score global dos usuários da plataforma
workspace.yelp_gold.user_behavior_complete - Análise completa de comportamento de usuários (global, por estado e por cidade)
3. Tabelas de Detecção de Inconsistências
workspace.yelp_gold.alertas_inconsistencia_base - Base detalhada com todos os reviews e marcação de possíveis inconsistências
workspace.yelp_gold.alerta_inconsistencia_usuarios - Agregação de alertas por usuário
workspace.yelp_gold.alerta_inconsistencia_estabelecimentos - Agregação de alertas por estabelecimento e data
workspace.yelp_gold.alerta_inconsistencia_consolidado_geral - Métricas consolidadas gerais por data
workspace.yelp_gold.alerta_inconsistencia_distribuicao_temporal - Distribuição temporal de reviews suspeitas por faixa horária