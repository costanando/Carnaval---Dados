
<!--
Instruções rápidas (resumo):
1. Abra o arquivo .pbix no Power BI Desktop.
2. Exporte as tabelas/dados para CSVs (ver instruções abaixo).
3. Coloque os CSVs dentro da pasta `data/` ao lado deste arquivo.
4. Abra este .Rmd no RStudio e clique em Knit -> HTML para gerar o relatório.
5. Publique o HTML no GitHub (push do repositório) ou ative GitHub Pages para hospedar.
-->

# Objetivo

Este relatório reproduz análises e visualizações a partir dos dados exportados do arquivo Power BI `Power B Carnaval 20.10.pbix`.

# Como exportar dados do PBIX (opções)

- **Exportar por tabela (Power BI Desktop)**: abra o PBIX, vá para a aba *Data* (Dados), selecione a tabela, clique com botão direito e escolha `Export data` (ou use o menu de três pontos no visual) e salve como CSV.
- **Usando DAX Studio**: instale e abra o DAX Studio, conecte ao modelo do Power BI e exporte as tabelas com `Export Data` — essa opção preserva tipos e garante exportes maiores.
- **External Tools / Tabular Editor**: também permitem extrair metadados e tabelas do modelo se necessário.

Coloque os CSVs exportados em `data/` com nomes legíveis, por exemplo: `data/tabela_vendas.csv`, `data/tabela_clientes.csv`.

---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, warning = FALSE, message = FALSE)
library(readr)
library(dplyr)
library(ggplot2)
```

# Carregar dados

A seguir há um exemplo que assume dois arquivos CSV: `tabela_vendas.csv` e `tabela_clientes.csv`.

```{r load-data}
# Ajuste os nomes dos arquivos conforme seus exports
vendas <- read_csv("data/tabela_vendas.csv")
clientes <- read_csv("data/tabela_clientes.csv")

# Visualizar colunas principais
glimpse(vendas)
glimpse(clientes)
```

# Limpeza e transformação (exemplo)

```{r transform}
# Exemplo: somar vendas por dia
vendas_por_dia <- vendas %>%
  mutate(data = as.Date(data)) %>%
  group_by(data) %>%
  summarise(total_vendas = sum(valor_venda, na.rm = TRUE),
            qtd_transacoes = n()) %>%
  arrange(data)

head(vendas_por_dia)
```

# Visualizações de exemplo

```{r plots, fig.width=10, fig.height=5}
# Linha de vendas por dia
ggplot(vendas_por_dia, aes(x = data, y = total_vendas)) +
  geom_line() +
  geom_point() +
  labs(title = "Vendas por dia",
       x = "Data",
       y = "Total (R$)") +
  theme_minimal()
```

# Tabela resumida pronta para GitHub

```{r table, echo=FALSE}
# Gerar tabela resumida e salvar como CSV para publicação
readr::write_csv(vendas_por_dia, "output/vendas_por_dia.csv")
knitr::kable(head(vendas_por_dia, 20), caption = "Vendas por dia (exemplo)")
```

# Como publicar no GitHub

1. Crie um repositório no GitHub e coloque neste repositório:
   - a pasta `data/` com os CSVs exportados;
   - este arquivo `.Rmd`;
   - uma pasta `output/` (onde serão geradas CSVs e o HTML de saída, se desejar).
2. No RStudio rode `Knit` para gerar o HTML (arquivo `PowerBI_to_RMarkdown_for_GitHub.html`).
3. Commit & push o HTML e os arquivos para o repositório.
4. (Opcional) Ative GitHub Pages nas configurações do repositório para hospedar o HTML como site.

# Observações e dicas finais

- Se o PBIX contém medidas DAX complexas, pode ser preciso recriar as métricas em R (dplyr) ou exportar resultados agregados diretamente do Power BI.
- Para automação, considere usar o **Power BI REST API** ou ferramentas como **DAX Studio** para extrair tabelas de forma programática.
- Se quiser, posso gerar scripts R adicionais para cada tabela específica assim que você exportar os CSVs (ou posso tentar extrair dados diretamente do `.pbix` se você confirmar que quer que eu tente — note que extrair do .pbix sem Power BI pode ser parcial).


----

*Gerado automaticamente — adapte nomes de arquivos e colunas conforme os dados exportados.*
