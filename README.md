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
