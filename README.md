# 💰 Financiamento Externo e o Motor Econômico Estadual (SP e RJ, 2010-2021)

Este projeto tem como objetivo principal analisar a relação e o impacto de **financiamentos externos** (programas multilaterais e empréstimos internacionais) em obras públicas sobre o **Produto Interno Bruto (PIB)** dos estados de São Paulo (SP) e Rio de Janeiro (RJ) entre 2010 e 2021.

A análise busca ir além de correlações simples, investigando a **alocação setorial** dos recursos, a influência de **grandes projetos isolados (outliers)** e a **defasagem temporal** dos impactos econômicos.

---

## 🎯 Objetivos e Hipóteses Centrais

O estudo avalia diversas hipóteses para mapear a complexa relação entre o volume de financiamento e a dinâmica econômica estadual:

* **H1 (PIB vs. Projetos):** A correlação entre o número de projetos financiados e o PIB é positiva, mas geralmente fraca no agregado.
* **H2 (PIB vs. Valor Total):** A correlação entre o valor total dos financiamentos e o PIB é positiva, mas pode ser não linear devido à presença de grandes projetos isolados.
* **H5 (Concentração Setorial):** Há concentração de recursos em setores estratégicos como **infraestrutura e serviços**.
* **H6 (Não Proporcionalidade):** A alocação de financiamentos não é proporcional ao tamanho do PIB do estado, sugerindo a influência de fatores políticos/institucionais.

---

## 🛠️ Metodologia e Pipeline de Dados (ETL)

O projeto utiliza **Python** (Pandas, Matplotlib/Seaborn) e **Excel** para tratamento, agregação e visualização de dados.

| Fonte de Dados | Conteúdo Principal |
| :--- | :--- |
| **Financiamento** (`dados_2025-02-05.xlsx`) | Valor em Dólar (`VlDolar`), Setor (`nmSetor`), Unidade da Federação (`nmUnidadeFederal`). |
| **PIB** (`PIB dos Municípios - base de dados 2010-2021.xlsx`) | PIB (R$ 1.000) e Componentes do Valor Adicionado Bruto (VAB) por setr e município. |

### 1. Tratamento e Agregação do PIB Estadual

* **Consolidação:** Dados municipais são agrupados para obter os valores totais anuais para SP e RJ.
* **Conversão de Unidade:** Colunas de PIB e VAB são convertidas de R$ 1.000 para **R$ (multiplicação por 1000)**.
* **Agregação:** Soma dos valores de todos os municípios por UF e Ano, totalizando o **PIB estadual** e VAB setorial.

### 2. Tratamento e Agregação de Financiamentos

* **Mapeamento e Data:** O nome do estado é mapeado para a sigla (SP/RJ). O ano do evento é determinado priorizando a data de recebimento (`dtRecebimento`).
* **Agregação:** O volume total em Dólar (`VlDolar_num`) e o número de projetos são somados/contados por UF e Ano.
* **Classificação Setorial:** Os setores são mapeados heuristicamente para grupos amplos (ex: `Industria_Infrastr`, `Servicos`) para a análise da H5.

### 📊 Resultados Obtidos (Gráficos)

A seguir estão as visualizações geradas pelo projeto.

Substitua os caminhos abaixo pelos arquivos das imagens no seu repositório.
Exemplo: img/grafico_rj1.png

## 1️⃣ Nº de Projetos e Volume de Financiamentos — RJ (2010-2021)

<img width="719" height="311" alt="image" src="https://github.com/user-attachments/assets/30ec12b4-15df-4231-abc1-b5775d7f6fe9" />

Gráfico mostra número de projetos por ano (barras) e volume agregado em R$ (barra/linha no eixo secundário).

Observação: grande pico de volume em um ano (ex.: 2018) indica um ou poucos projetos muito caros — um outlier.

Implicação: correlação entre volume e PIB pode ser distorcida por esses grandes projetos; analisar com e sem outlier é recomendável.

## 2️⃣ Nº de Projetos e Volume de Financiamentos — SP (2010-2021)

<img width="718" height="304" alt="image" src="https://github.com/user-attachments/assets/f265c6e2-14c6-433b-85e2-ec097b411af3" />

SP tem mais projetos distribuídos ao longo dos anos; em alguns anos há picos de valor (ex.: 2013 e 2018).

Pode indicar que SP recebe mais projetos em número, mas volume por projeto varia muito.

Teste: calcular média e mediana do valor por projeto por ano (mediana menos sensível a outliers).

## 3️⃣ Evolução Setorial do VAB — SP

<img width="470" height="308" alt="image" src="https://github.com/user-attachments/assets/ecba635a-3195-4a53-99fa-b00a86b7b1cd" />

Mostra crescimento consistente do setor de serviços (VAB_Serv), seguido pela indústria e administração/saúde.

Interpretação: economia de SP é fortemente guiada por serviços (setor principal de geração de VAB).

Relevância: se financiamentos se concentram em infraestrutura/energia e não em serviços, pode haver desalinhamento entre setores que geram PIB e onde os financiamentos vão.

## 4️⃣ Evolução Setorial do VAB — RJ

<img width="478" height="305" alt="image" src="https://github.com/user-attachments/assets/021ffb13-b552-4f6d-ae6c-cc7a2d33c7a6" />

RJ também mostra setor de serviços dominante, mas com participação maior de indústria em alguns anos.

Observa-se crescimento no componente VAB_Ind após 2016 e um grande salto em financiamentos para indústria em 2021 (possível grande projeto).

Novamente, um grande projeto pode distorcer a relação.

## 5️⃣ PIB x Financiamentos — SP (2010-2021)

<img width="568" height="313" alt="image" src="https://github.com/user-attachments/assets/bdde4027-d131-4331-bb79-cb655a9bf26e" />

Linha do PIB tem tendência clara de crescimento de 2010–2021.

Financiamentos mostram picos isolados (2013, 2018), com anos de financiamento quase nulos entre eles.

Isso sugere que impactos imediatos no PIB podem ser discretos e defasados. É necessário analisar lags (ex.: efeito de financiamento sobre o PIB 1–3 anos depois).

## 6️⃣ PIB x Financiamentos — RJ (2010-2021)

<img width="568" height="315" alt="image" src="https://github.com/user-attachments/assets/75bf7029-be97-4568-b1c4-31d20e949ae5" />

Linha do PIB tem tendência clara de crescimento de 2010–2021.

Financiamentos mostram picos isolados (2013, 2018), com anos de financiamento quase nulos entre eles.

Essa dinâmica de choques pontuais de investimento reforça a sugestão de que impactos imediatos no PIB podem ser discretos e defasados. É necessário analisar lags (ex.: efeito de financiamento sobre o PIB 1–3 anos depois) para capturar o efeito multiplicador completo.
