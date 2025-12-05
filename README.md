# Financiamento-Externos-em-obras-p-blicas-e-seus-impactos-no-PIB-estadual-do-Brasil

📊 Análise de Financiamentos Externos e PIB Estadual (SP e RJ, 2010-2021)
Este projeto tem como objetivo principal analisar a relação e o impacto dos financiamentos externos (como programas multilaterais e empréstimos internacionais) em obras públicas sobre o Produto Interno Bruto (PIB) dos estados de São Paulo (SP) e Rio de Janeiro (RJ) entre os anos de 2010 e 2021.


A análise busca ir além de correlações simples, investigando a alocação setorial dos recursos, a influência de grandes projetos isolados (outliers) e a defasagem temporal dos impactos econômicos.


🎯 Objetivos e Hipóteses
O estudo avalia diversas hipóteses (H1-H6) para mapear a complexa relação entre o volume de financiamento e a dinâmica econômica estadual:


H1 (PIB vs. Nº de Projetos): A correlação entre o número de projetos financiados e o PIB é positiva (mas geralmente fraca no agregado).



H2 (PIB vs. Valor Total): A correlação entre o valor total dos financiamentos e o PIB é positiva. A relação pode se mostrar não linear devido à presença de grandes projetos isolados (outliers).



H5 (Concentração Setorial): Há concentração de recursos em setores estratégicos como infraestrutura e serviços.



H6 (Não Proporcionalidade): A alocação de financiamentos não é proporcional ao tamanho do PIB do estado, sugerindo a influência de fatores políticos/institucionais.


🛠️ Metodologia e Conjunto de Dados
O projeto utiliza a linguagem Python e Excel para realizar a limpeza das bases de dados com a biblioteca Pandas para tratamento e agregação de dados, e Matplotlib/Seaborn para visualização.

💾 Fontes de Dados
Dados de Financiamento: Arquivo dados_2025-02-05.xlsx. Contém informações detalhadas sobre projetos de investimento, incluindo:

nmProjeto, nmSetor (Nome do Projeto, Setor)

nmUnidadeFederal (Unidade da Federação)

VlDolar (Valor em Dólar)

Dados de PIB: Arquivo PIB dos Municípios - base de dados 2010-2021.xlsx. Contém dados do PIB municipal, incluindo:

Sigla da Unidade da Federação

Ano

Produto Interno Bruto, a preços correntes (R$ 1.000) (PIB)

Componentes do Valor Adicionado Bruto (VAB) por setor.

💻 Detalhes do Código
O script realiza as seguintes etapas de ETL (Extração, Transformação, Carga) e Análise para SP e RJ.

1. Tratamento e Agregação do PIB Estadual
O objetivo desta seção é consolidar os dados de PIB e VAB dos municípios para obter os valores totais anuais para os estados de SP e RJ.

a. Leitura e Conversão de Unidade
As colunas de PIB e VAB (Valor Adicionado Bruto) são lidas e convertidas de R$ 1.000 para R$ (multiplicando por 1000).

Python

# Leitura e Filtragem inicial
pib = pib_df.copy()
pib_states = pib[pib["UF_sigla"].isin(["SP","RJ"])].copy()

# Conversão da unidade (R$ 1.000 -> R$) para o PIB
pib_states["PIB_valor"] = pd.to_numeric(pib_states["PIB_valor_1000"], errors="coerce") * 1000
# Exemplo de conversão para VAB Indústria
pib_states["VAB_Ind"] = pd.to_numeric(pib_states["VAB_Ind_1000"], errors="coerce") * 1000
# ... (demais conversões de VAB)
b. Agregação do PIB Total
O agrupamento por UF e Ano soma os valores de todos os municípios, fornecendo o PIB estadual por ano.

Python

# Agregação por UF e Ano (soma de municípios -> total estadual)
pib_agg = pib_states.groupby(["UF_sigla","Ano"], as_index=False)[
    ["PIB_valor","VAB_Agro","VAB_Ind","VAB_Serv","VAB_AdminSaude"]
].sum()
2. Tratamento e Agregação de Financiamentos
Esta etapa padroniza os dados de financiamento, determinando o ano do evento e o volume total em dólares por estado.

a. Mapeamento de Estado e Ano
Funções são utilizadas para mapear o nome completo do estado para a sigla (SP/RJ) e extrair o Ano do registro, priorizando a data de recebimento (dtRecebimento).

Python

# Função heurística para mapear nome do estado para sigla (SP, RJ)
def map_state(name):
    # ... [lógica de mapeamento] ...
    if "são paulo" in s or "sao paulo" in s:
        return "SP"
    if "rio de janeiro" in s:
        return "RJ"
    return None

fin["UF_sigla"] = fin["nmUnidadeFederal"].apply(map_state)

# Determinação do Ano: usa dtRecebimento e preenche com outras datas se ausente
fin["data_evento"] = fin.get("dtRecebimento_dt").fillna(fin.get("dtAssinatura_dt")).fillna(fin.get("dtAprovacaoSenado_dt")).fillna(fin.get("dtAprovDiretoria_dt"))
fin["Ano"] = fin["data_evento"].dt.year

# Agrega o volume total e conta o número de projetos
fin_agg = fin_states.groupby(["UF_sigla","Ano"], as_index=False)["VlDolar_num"].sum()
b. Classificação Setorial (Para H5)
Os nomes dos setores são mapeados para grupos mais amplos, como Industria_Infrastr (Indústria/Infraestrutura) e Servicos (Serviços, Turismo, Saneamento, etc.), para a análise de concentração setorial.

Python

# Mapeamento heurístico de setores (para H5)
sector_map = {
    "Agro": ["agric", "agro", "agricultura"],
    # ...
    "Servicos": ["comércio", "serviço","turismo","saneamento","desenvolvimento social"],
    "Industria_Infrastr": ["energia","logística","transporte","infraestrutura","crédito"],
}

fin_states["sector_group"] = fin_states["nmSetor"].apply(map_sector)
fin_sector_agg = fin_states.groupby(["UF_sigla","Ano","sector_group"], as_index=False)["VlDolar_num"].sum()
3. Visualização: PIB x Financiamentos (H2)
Um gráfico de duplo eixo compara o crescimento do PIB (R$ bilhões) com o volume de financiamentos (R$ milhões, após conversão cambial) para SP e RJ.

Python

# Gráfico PIB x Financiamentos (SP/RJ)
for uf in ["SP","RJ"]:
    # ... [filtra e prepara os DataFrames p_pib e p_fin] ...
    
    # Exemplo de conversão: (assumindo cotação fixa de exemplo)
    cotacao = 3.29  
    valor_brl = p_fin["VlDolar_num"] * cotacao

    fig, ax1 = plt.subplots(figsize=(9,5))
    
    # PIB (Eixo Y1, R$ bilhões)
    ax1.plot(p_pib.index, p_pib["PIB_valor"]/1e9, marker="o", label="PIB (R$ bi)")
    ax1.set_ylabel("PIB (R$ bilhões)")

    # Financiamentos (Eixo Y2, R$ milhões)
    ax2 = ax1.twinx()
    ax2.plot(p_fin.index, valor_brl/1e6, marker="s", color="C1", label="Financiamentos (R$ mi)")
    ax2.set_ylabel("Financiamentos (R$ milhões)")
    
    ax1.set_title(f"PIB x Financiamentos - {uf} (2010-2021)")
    # ... [configurações de grade e legenda] ...
    plt.show()

📊 Resultados Obtidos (Gráficos)

A seguir estão as visualizações geradas pelo projeto.

Substitua os caminhos abaixo pelos arquivos das imagens no seu repositório.
Exemplo: img/grafico_rj1.png

1️⃣ Nº de Projetos e Volume de Financiamentos — RJ (2010-2021)

<img width="719" height="311" alt="image" src="https://github.com/user-attachments/assets/30ec12b4-15df-4231-abc1-b5775d7f6fe9" />

Gráfico mostra número de projetos por ano (barras) e volume agregado em R$ (barra/linha no eixo secundário).

Observação: grande pico de volume em um ano (ex.: 2018) indica um ou poucos projetos muito caros — um outlier.

Implicação: correlação entre volume e PIB pode ser distorcida por esses grandes projetos; analisar com e sem outlier é recomendável.

2️⃣ Nº de Projetos e Volume de Financiamentos — SP (2010-2021)

<img width="718" height="304" alt="image" src="https://github.com/user-attachments/assets/f265c6e2-14c6-433b-85e2-ec097b411af3" />

SP tem mais projetos distribuídos ao longo dos anos; em alguns anos há picos de valor (ex.: 2013 e 2018).

Pode indicar que SP recebe mais projetos em número, mas volume por projeto varia muito.

Teste: calcular média e mediana do valor por projeto por ano (mediana menos sensível a outliers).

3️⃣ Evolução Setorial do VAB — SP

<img width="470" height="308" alt="image" src="https://github.com/user-attachments/assets/ecba635a-3195-4a53-99fa-b00a86b7b1cd" />

Mostra crescimento consistente do setor de serviços (VAB_Serv), seguido pela indústria e administração/saúde.

Interpretação: economia de SP é fortemente guiada por serviços (setor principal de geração de VAB).

Relevância: se financiamentos se concentram em infraestrutura/energia e não em serviços, pode haver desalinhamento entre setores que geram PIB e onde os financiamentos vão.

4️⃣ Evolução Setorial do VAB — RJ

<img width="478" height="305" alt="image" src="https://github.com/user-attachments/assets/021ffb13-b552-4f6d-ae6c-cc7a2d33c7a6" />

RJ também mostra setor de serviços dominante, mas com participação maior de indústria em alguns anos.

Observa-se crescimento no componente VAB_Ind após 2016 e um grande salto em financiamentos para indústria em 2021 (possível grande projeto).

Novamente, um grande projeto pode distorcer a relação.

5️⃣ PIB x Financiamentos — SP (2010-2021)

<img width="568" height="313" alt="image" src="https://github.com/user-attachments/assets/bdde4027-d131-4331-bb79-cb655a9bf26e" />

Linha do PIB tem tendência clara de crescimento de 2010–2021.

Financiamentos mostram picos isolados (2013, 2018), com anos de financiamento quase nulos entre eles.

Isso sugere que impactos imediatos no PIB podem ser discretos e defasados. É necessário analisar lags (ex.: efeito de financiamento sobre o PIB 1–3 anos depois).

6️⃣ PIB x Financiamentos — RJ (2010-2021)

<img width="568" height="315" alt="image" src="https://github.com/user-attachments/assets/75bf7029-be97-4568-b1c4-31d20e949ae5" />

Linha do PIB tem tendência clara de crescimento de 2010–2021.

Financiamentos mostram um pico isolado (2018), com anos de financiamento quase nulos.

Isso sugere que impactos imediatos no PIB podem ser discretos e defasados. É necessário analisar lags (ex.: efeito de financiamento sobre o PIB 1–3 anos depois).


Para fins de Comprovação da publicação do projeto abaixo ficará a carta de aceite do congresso onde o projeto foi publicado:

[Carta de Aceite.pdf](https://github.com/user-attachments/files/23830774/Carta.de.Aceite.pdf)
