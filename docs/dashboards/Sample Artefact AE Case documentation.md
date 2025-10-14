# Documentação do Modelo Power BI - Sample Artefact AE Case

## Visão Geral

Este é um modelo de análise de vendas da **Company NiceBikes**, que vende diferentes tipos de bicicletas em múltiplos países e canais, atendendo segmentos como Midmarket e Government.

## Arquitetura do Modelo

### Estrutura Star Schema

```
                    dimDate
                       |
                       |
    dimDiscountBand ---|--- fact_Sales (Tabela Fato Central)
                       |
                       |
                Base_2013_2014 (oculta)
                Base_2015_2016 (oculta)
                _Measures (medidas DAX)
```

## Tabelas do Modelo

### 1. fact_Sales (Tabela Fato Principal)

**Propósito**: Tabela central com dados transacionais de vendas

**Fonte de Dados**: Combinação das tabelas `Base_2013_2014` e `Base_2015_2016`

**Campos Principais**:

#### Dimensões (Atributos)
- `Segment` (string): Segmento de negócio (ex: Midmarket, Government)
- `Country` (string): País da venda
- `Product` (string): Produto vendido (tipos de bicicletas)
- `Discount Band` (string): Faixa de desconto aplicada

#### Métricas Numéricas
- `Units Sold` (int64): Quantidade de unidades vendidas
- `Manufacturing Price` (int64): Preço de fabricação
- `Sale Price` (int64): Preço de venda
- `Gross Sales` (int64): Vendas brutas
- `Discounts` (double): Valor dos descontos aplicados
- `Sales` (double): Vendas líquidas
- `COGS` (int64): Custo dos produtos vendidos
- `Profit` (double): Lucro obtido

#### Campos Temporais
- `Date` (dateTime): Data da transação (oculto - usa dimensão separada)
- `Year` (int64): Ano da transação
- `Month Number` (int64): Número do mês (oculto)
- `Month Name` (string): Nome do mês (oculto)

#### Campos Calculados
- `Test Column`: Coluna calculada usando DAX: `SUMX(fact_Sales, fact_Sales[Sales] - fact_Sales[COGS])`

### 2. dimDate (Dimensão Temporal)

**Propósito**: Dimensão de tempo para análises temporais e time intelligence

**Tipo**: Tabela calculada

**Fórmula DAX**: `CALENDAR(min(fact_Sales[Date]),max(fact_Sales[Date]))`

**Campos**:
- `Date` (dateTime): Data base
- `Year` (int64): Ano extraído da data
- `Month number` (int64): Número do mês (1-12)
- `Month` (string): Nome abreviado do mês (Jan, Feb, etc.)
- `Quarter` (string): Trimestre no formato "Q1", "Q2", etc.

**Funcionalidades**:
- Permite análises de tendências temporais
- Habilita funções de time intelligence
- Suporte para comparações período a período

### 3. dimDiscountBand (Dimensão de Faixas de Desconto)

**Propósito**: Dimensão para categorizar e ordenar corretamente as faixas de desconto

**Campos**:
- `Discount Band` (string): Nome da faixa de desconto
- `Discount Band Order` (int64): Ordem numérica para classificação correta

**Funcionalidade**: Permite análise ordenada das faixas de desconto em visualizações

### 4. Base_2013_2014 e Base_2015_2016 (Tabelas Fonte - Ocultas)

**Propósito**: Tabelas de origem dos dados históricos

**Status**: Marcadas como `isHidden` - não aparecem para usuários finais

**Fonte de Dados**: 
- Arquivos Excel hospedados no GitHub
- URL: `https://raw.githubusercontent.com/henriquetoledos/pbi-demo-artefact/main/data/Base_de_dados_2013_2014.xlsx`

**Transformações Aplicadas**:
- Promoção de cabeçalhos
- Transformação de tipos de dados
- Remoção de colunas desnecessárias
- Padronização de nomes de países (ex: "United States of America" → "USA")

### 5. _Measures (Tabela de Medidas)

**Propósito**: Centraliza todas as medidas DAX calculadas do modelo

**Medidas Disponíveis**:

#### Margens e Rentabilidade
- **Profit Margin**: `SUM(fact_Sales[Profit]) / SUM(fact_Sales[Sales])`
  - Formato: Percentual
  - Descrição: Margem de lucro sobre vendas

- **Pricing Margin**: `(AVERAGE(fact_Sales[Sale Price]) - AVERAGE(fact_Sales[Manufacturing Price])) / AVERAGE(fact_Sales[Manufacturing Price])`
  - Formato: Percentual
  - Descrição: Margem de preço sobre custo de fabricação

#### Análise de Descontos
- **Discount rate**: `SUM(fact_Sales[Discounts]) / SUM(fact_Sales[Gross Sales])`
  - Formato: Percentual
  - Descrição: Taxa de desconto sobre vendas brutas

#### Análises Temporais - Mês a Mês (MoM)
- **Sales Last month**: Vendas do mês anterior usando `PARALLELPERIOD`
- **Sales MoM Var**: `SUM(fact_Sales[Sales]) - [Sales Last month]`
  - Formato: Moeda
  - Descrição: Variação absoluta mês a mês

- **Sales MoM %**: `DIVIDE([Sales MoM Var], [Sales Last month])`
  - Formato: Percentual
  - Descrição: Variação percentual mês a mês

#### Análises Temporais - Ano a Ano (YoY)
- **Sales Last year**: Vendas do ano anterior usando `PARALLELPERIOD`
- **Sales YoY Var**: `SUM(fact_Sales[Sales]) - [Sales Last year]`
  - Formato: Moeda
  - Descrição: Variação absoluta ano a ano

- **Sales YoY %**: `DIVIDE([Sales YoY Var], [Sales Last year])`
  - Formato: Percentual
  - Descrição: Variação percentual ano a ano

## Relacionamentos

### 1. fact_Sales ↔ dimDate
- **Tipo**: Muitos para Um (Many-to-One)
- **Campos**: `fact_Sales[Date]` → `dimDate[Date]`
- **Propósito**: Permite análises temporais e time intelligence

### 2. fact_Sales ↔ dimDiscountBand
- **Tipo**: Muitos para Um (Many-to-One)
- **Campos**: `fact_Sales[Discount Band]` → `dimDiscountBand[Discount Band]`
- **Propósito**: Permite análise ordenada das faixas de desconto

## Configurações Técnicas

### Configurações Gerais
- **Cultura**: pt-BR (português brasileiro)
- **Versão Power BI**: 2.144.878.0
- **Modo de Importação**: Import para todas as tabelas
- **Nível de Compatibilidade**: 1567

### Time Intelligence
- **Status**: Habilitado através da dimensão `dimDate`
- **Funções Disponíveis**: PARALLELPERIOD, comparações MoM e YoY

### Fonte de Dados
- **Tipo**: Arquivos Excel via Web
- **Localização**: GitHub (repositório público)
- **Atualização**: Manual via Power Query

## Casos de Uso e Análises Possíveis

### 1. Análise de Performance de Vendas
- Vendas por segmento de mercado
- Performance por país e região
- Análise de produtos mais vendidos
- Tendências de unidades vendidas

### 2. Análise de Rentabilidade
- Margem de lucro por produto/segmento/país
- Comparação entre preço de venda e custo de fabricação
- Análise de COGS (Cost of Goods Sold)
- ROI por categoria

### 3. Análise de Descontos
- Impacto dos descontos nas vendas
- Efetividade das faixas de desconto
- Correlação entre desconto e volume
- Análise de elasticidade de preço

### 4. Análises Temporais
- Tendências de crescimento MoM e YoY
- Sazonalidade das vendas
- Comparações período a período
- Previsões baseadas em histórico

### 5. Análise Geográfica
- Performance por país
- Comparação de mercados
- Análise de penetração geográfica

## Boas Práticas Implementadas

### Modelagem
- ✅ Estrutura Star Schema
- ✅ Separação de dimensões e fatos
- ✅ Tabelas fonte ocultas
- ✅ Medidas centralizadas em tabela dedicada

### Performance
- ✅ Modo Import para melhor performance
- ✅ Relacionamentos otimizados
- ✅ Campos desnecessários ocultos

### Usabilidade
- ✅ Formatação adequada de medidas
- ✅ Ordenação correta de dimensões
- ✅ Nomes descritivos para campos

## Manutenção e Atualizações

### Atualização de Dados
- Dados são atualizados via Power Query
- Fonte: Arquivos Excel no GitHub
- Processo: Manual (pode ser automatizado)

### Versionamento
- Modelo versionado via PBIP (Power BI Project)
- Controle de versão através de Git
- Histórico de mudanças rastreável

---

**Última Atualização**: Junho 2025  
**Versão do Documento**: 1.0  
**Responsável**: Análise automatizada do modelo Power BI
