# Projeto: Auditoria de Classificação de Materiais (Data Quality)

Este projeto é uma solução de automação e Data Quality (Qualidade de Dados) desenvolvida em Python para resolver um desafio central em Suprimentos: garantir que os materiais no sistema (ERP/SAP) estejam classificados com o Grupo de Mercadoria (GM) correto.

## 🎯 O Problema de Negócio

O catálogo de materiais (`Zdmat0425`) possui um "Grupo de mercadorias" que foi preenchido manualmente ao longo do tempo. Muitas dessas classificações são antigas, incorretas ou inconsistentes. Isso prejudica análises de gastos (spend analysis), negociações com fornecedores e a gestão de estoque.

O objetivo é **auditar** o catálogo inteiro e **identificar** quais materiais estão classificados de forma errada, sugerindo a correção.

## 🛠️ A Solução Técnica

A solução foi construir um "robô auditor" em Python/Pandas que implementa as regras de negócio de forma automática. O script funciona em duas fases:

### Parte 1: Classificação por Keyword Matching (Marca/Referência)

1.  **Carga de Dados:** O script carrega a base principal de materiais (`Zdmat0425`) e uma tabela-dicionário (`Tabela`) que serve como "fonte da verdade". Essa tabela mapeia milhares de palavras-chave (como Fabricantes, Marcas ou Referências Comerciais) ao seu "Grupo de Mercadoria" (GM) correto.
2.  **Motor de Busca (Loop):** O código itera por *cada palavra-chave* do dicionário e varre a base de materiais inteira (colunas "Material" e "Texto Longo") procurando por ela.
3.  **Atribuição:** Quando uma palavra-chave é encontrada em um material pela primeira vez, o script "carimba" aquele material com a classificação correta (`GM Palavra`) e as regras associadas (`REGRA ATUAL`).

### Parte 2: O Motor de Auditoria (Função `verificar_status`)

Esta é a inteligência central do projeto. Após a Parte 1, temos duas informações para cada material:
* O GM *existente* no sistema (antigo).
* O `GM Palavra` (o GM *correto* encontrado pelo script).

A função `verificar_status` compara os dois e gera um status de ação:

* **(Vazio):** Se o script não encontrou nenhuma palavra-chave para aquele item.
* **"OK":** Se o GM existente no sistema **está correto** (bate com o `GM Palavra` e respeita as regras).
* **"Modificar para [NOVO_GM]":** Se o GM existente no sistema **está errado**. O script automaticamente sugere a classificação correta.

## 🚀 Resultado Final

O script gera um arquivo Excel (`Regras_status.xlsx`) que é um **relatório de ação** para a equipe de Data Quality ou Suprimentos. A equipe pode filtrar a coluna "Status" por "Modificar para..." e realizar as correções no sistema SAP, garantindo um catálogo de materiais limpo e padronizado.

Em uma execução de teste, o script analisou **X** linhas e identificou **Y** linhas que precisavam de correção.

## 💻 Tecnologias Utilizadas
* **Python**
* **Pandas:** Para toda a carga, transformação, busca e merge dos dados.
* **NumPy:** Para inicialização e tratamento de valores nulos (`np.nan`).
* **Lógica de Regras:** Para a implementação da função de auditoria `verificar_status`.
