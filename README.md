# NovaDrive | End-to-End Data Engineering Pipeline

Projeto prático de Engenharia de Dados desenvolvido em um bootcamp, utilizando um cenário educacional de vendas de veículos. O objetivo é transformar dados transacionais em informações analíticas, integrando ingestão, orquestração, Data Warehouse, modelagem dimensional, testes e visualização.

> **Escopo:** projeto de aprendizado, não uma implantação em produção. Os valores apresentados no dashboard são dados do exercício, não resultados financeiros gerados para uma empresa.

## Arquitetura

```text
PostgreSQL (7 entidades)
       |
       v
Apache Airflow + Python (AWS EC2 / Docker)
       |
       v
Snowflake (NOVADRIVE.STAGE)
       |
       v
dbt: staging -> dimensions -> facts -> analysis
       |
       v
Looker Studio (dashboard comercial)
```

**Stack:** Python, PostgreSQL, AWS EC2, Linux, Docker Compose, Apache Airflow, Snowflake, dbt, SQL/Jinja, Git/GitHub e Looker Studio.

## Implementação

- **Ingestão:** DAG `postgres_to_snowflake`, com tarefas de consulta do maior ID no destino e carregamento de registros novos para sete tabelas: `veiculos`, `estados`, `cidades`, `concessionarias`, `vendedores`, `clientes` e `vendas`.
- **Transformação:** sete modelos de staging, seis dimensões, uma fato de vendas incremental e quatro modelos de análise (vendedor, concessionária, veículo e período).
- **Qualidade:** teste SQL que identifica vendas fora da faixa de 95% a 100% do preço sugerido do veículo.
- **Consumo:** dashboard com indicadores de vendas, filtros e distribuição geográfica.
- **Versionamento:** código dbt mantido em GitHub, com trabalho em branches e Pull Requests.

## Resultado demonstrativo

No dashboard do exercício foram exibidos aproximadamente **R$ 452,8 milhões em valor de vendas** e **1.320 transações**. Esses números descrevem o conjunto de dados do curso e não representam impacto econômico atribuído à implementação.

![Dashboard NovaDrive](project-docs\dashboard.png)

## Estrutura do repositório

| Caminho | Conteúdo |
| --- | --- |
| `models/stage/` | Padronização das fontes transacionais |
| `models/dimensions/` | Seis dimensões comerciais e geográficas |
| `models/facts/` | Fato de vendas incremental |
| `models/analysis/` | Quatro modelos agregados para análise |
| `models/source.yml` | Declaração das sete tabelas de origem no Snowflake |
| `tests/test.sql` | Regra de validação de preço de venda |
| `project-docs/` | DAG, anotações de infraestrutura, diagramas e imagens |

## Decisões e limitações

O carregamento incremental usa o maior identificador presente no destino. Essa estratégia contempla novas linhas com IDs superiores, mas **não captura alterações ou exclusões de registros existentes**. A DAG documentada utiliza inserções individuais no Snowflake e não implementa retries; por isso, otimização de carga, idempotência, tratamento de falhas e CDC são oportunidades de evolução.

O arquivo `project-docs/dag-airflow.txt` registra a implementação original do curso, incluindo `schedule_interval`. Para execução em Airflow 3, essa configuração foi adaptada no ambiente de estudo para `schedule`. A atualização do arquivo versionado permanece pendente.

## Documentação completa

Leia o **[estudo de caso técnico e STAR](project-docs/CASE_STUDY.md)** para conhecer o problema, a implementação, os resultados e os próximos passos.

### Evidências visuais

- [Modelo entidade-relacionamento](project-docs/modelo-entidade-relacionamento.png)
- [Modelo dbt](project-docs/dbt-models/data%20model%20dbt.png)
- [Stack de ferramentas](project-docs/stack-ferramentas.png)

---

**Autor:** Samuel Pacheco · **Natureza:** projeto educacional de Engenharia de Dados.
