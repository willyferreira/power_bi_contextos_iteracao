## Curso Alura | Power BI: Contextos e iteração
O curso teve como ensinamentos os seguintes:

- Criação de colunas calculadas e medidas
- Lidando com erros no DAX
- Quando utilizar colunas calculadas e medidas
- Conceito de agregação
- Contexto de filtro e de linha
- Compreensão de funções iteradoras no DAX
- Utilizar variáveis com DAX

Nesse repositório, há também um notebook em Python onde realizei a análise exploratória dos datasets como forma de conhecer melhor os dados, promover o tratamento dos dados para simular situações reais enfrentadas no mercado de trabalho e gerar algumas visualizações simples.

### DAX
No Power BI é essencial o entendimento dos contextos de linha e de filtro para decidir a criação de colunas calculadas ou medidas, pensando principalmente no desempenho do relatório visto que o cálculo linha a linha das tabelas gera maior necessidade de processamento e consequentemente maior tempo para carregamento do relatório.

Sendo assim, foram criadas colunas calculadas inicialmente com operadores simples, como o de multiplicação **(*)**, para multiplicar a quantidade de vendas pelo valor unitário a fim de obter o faturamento total por produto, por exemplo.

#### Coluna calculada
```
Faturamento total = 'Livros'[Quantidade de vendas] * 'Livros'[Preço Unitário]
```

Esse problema pode ser resolvido pelas funções iteradoras, como **SUMX** e **AVERAGEX**, por exemplo. A coluna calculada acima, pode ser substituída pela criação de uma medida com a fórmula **SUMX**, que fará a mesma operação com desempenho melhor.

#### Função iteradora
```
FaturamentoTotal = SUMX(Livros, Livros[Preço Unitário] * Livros[Quantidade de vendas])
```

```
Média Leadtime Iterando = AVERAGEX(Vendas, INT(Vendas[Data_Entrega] - Vendas[Data_Compra]))
```

#### Utilização de variáveis
Outra funcionalidade muito importante no Power BI é a utilização de variáveis nas medidas, aumentando o desempenho do relatório e dando mais liberdade para criação de métricas condicionais.
Para cálculo da margem bruta em percentual, utilizei 3 variáveis dentro de uma medida e retornei o resultado.

**Medida com utilização de variáveis**
```
Margem bruta % = 
    VAR FaturamentoTotal = SUMX(Livros, Livros[Preço Unitário] * Livros[Quantidade de vendas])
    VAR CustoTotal = SUMX(Livros, Livros[Preço de custo] * Livros[Quantidade de vendas])
    VAR MargemBruta = FaturamentoTotal - CustoTotal
    RETURN
        MargemBruta / FaturamentoTotal
```

#### Fórmulas condicionais
Possibilidade de classificação e segmentação da base utilizando fórmulas condicionais como **IF** e **SWITCH**, além do tratamento de erros como **IFERROR**.

```
Classificação da margem = 
    IF([Margem bruta %] > 0.4,
    "Margem alta",
    "Margem baixa"
    )
```

```
Classificação de faturamento = 
    SWITCH(
        TRUE(), -- Retornar a primeira condição verdadeira
        'Livros'[Faturamento total] > 20000, "Faturamento alto",
        'Livros'[Faturamento total] > 15000, "Faturamento médio",
        "Faturamento baixo"
    )
```
### Python
O objetivo de fazer a análise de dados usando Python em um curso de Power BI é de verificar que no contexto da análise de dados, podemos enfrentar alguns obstáculos de acordo com a ferramenta utilizada. 

#### Tratamento dos dados
O Power BI propõe várias ferramentas simples para transformar dados de maneira interativa, porém, ao importar a mesma base no Python verifiquei por exemplo que os valores expressados em dinheiro no dataset deveriam ser modificados para que fosse possível realizar operações matemáticas, pois, os campos estavam preenchidos com "R$". Além disso foi necessário substituir a vírgula (,) por (.) padrão de números decimais do Python.

```
# Retirando o R$ da variável 
df_livros['Preço Unitário'] = df_livros['Preço Unitário'].str.replace('R$', '')

#Fazendo o mesmo tratamento para a variável preço de custo
df_livros['Preço de custo'] = df_livros['Preço de custo'].str.replace('R$', '')
```

Por final, se fez necessária a transformação do tipo da variável em *float*.

```
#Alterando o formato da variável para float
df_livros['Preço de custo'] = df_livros['Preço de custo'].astype(float)
```

#### Análise exploratória
Após o tratamento, chegou a hora de analisar os dados.
Por exemplo: 

**Quais são os livros mais vendidos?**

```
#Cria a visualização dos top 10
df_livros.sort_values(by = 'qtd_vendas', ascending = False).head(10)

#Cria o dataframe com a visualização
df_top10vendas = df_livros.sort_values(by = 'qtd_vendas', ascending = False).head(10)

#Seta o índice para o range iniciado em 1 e finalizado pelo total do range + 1
df_top10vendas.index = range(1, len(df_top10vendas) + 1)

#Reseta o índice e cria coluna com rank
df_top10vendas.reset_index(inplace = True)

#Renomeia a coluna pra ranking
df_top10vendas.rename(columns = {'index' : 'ranking'}, inplace = True)

#Mostra o dataframe
df_top10vendas
```

```
# Criação da tabela Top 10 - Vendas
fig = ff.create_table(df_top10vendas[['ranking', 'titulo', 'qtd_vendas']], height_constant = 20)
fig.show()
```

![Top 10 - Quantidade de vendas](https://github.com/willyferreira/power_bi_contextos_iteracao/blob/6ab7076880d259e7de1c9bb935e1a232c27f7dbc/images/top10_qtdvendas.png)

##### Visualizações
Utilizei a biblioteca Seaborn para visualizar algumas estatísticas de maneira mais simplificada.

**Como é a distribuição do preço dos livros?**