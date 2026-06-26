# Buscador-de-Precos-Google-Sheets

> Nome atual no GitHub: `Buscador-de-Preços-`. Sugestão de nome mais descritivo (sem acentos/caractere final solto): **`Buscador-de-Precos-Google-Sheets`**.

Scripts de **Google Apps Script** que automatizam a coleta e análise de preços de produtos na internet diretamente dentro de uma planilha do **Google Sheets**.

A partir de uma lista de produtos na coluna A, o sistema busca preços na web (via API do **Serper** — resultados de busca/shopping do Google) e, quando necessário, usa **IA** (Gemini ou Perplexity) para extrair preços de resultados orgânicos. Os preços coletados são filtrados (remoção de outliers) e estatísticas (média, mediana, máximo) são escritas automaticamente na planilha.

## Versões disponíveis

O repositório contém **3 implementações alternativas**, cada uma testando uma combinação diferente de API/custo-benefício. Todas compartilham a mesma lógica geral (loop por linha, execução em ciclos com trigger, filtragem de outliers), mas diferem na fonte de extração de preço.

| Arquivo | Estratégia | APIs usadas |
|---|---|---|
| `Buscador+serper.dev` | Busca preços apenas via Serper (Google Shopping/Search), com lista fixa de lojas preferidas e filtro de marketplaces (Mercado Livre, OLX, Shopee). | Serper |
| `API Gemini + Serper.dev` | Busca via Serper primeiro (Shopping); se encontrar menos de 2 preços, recorre à busca orgânica e usa o Gemini para extrair preços dos snippets de texto. | Serper + Gemini |
| `PERPLEXITY_API` | Usa a Perplexity API (que já tem acesso direto à web) para pesquisar e extrair preços diretamente, sem depender do Serper. | Perplexity |

## Como funciona (fluxo comum às versões com Serper)

1. `iniciarPesquisa()` — ponto de entrada. Cancela triggers pendentes, identifica a primeira linha ainda não processada e pergunta ao usuário (via `ui.alert`) se deve continuar a partir dali.
2. `executarCiclo()` — processa as linhas da planilha em lote, respeitando um limite de tempo por execução (`LIMITE_MS`, entre 5 e 9 minutos) para não exceder o limite de execução do Apps Script (6 min por trigger simples). Quando o tempo se esgota, salva a linha atual em `PropertiesService` e agenda automaticamente o próximo ciclo via `ScriptApp.newTrigger(...).after(60 * 1000)`.
3. Para cada produto: busca os preços (`buscarEExtrair`), filtra outliers pelo método de **IQR** (intervalo interquartil), calcula média/mediana/máximo/quantidade e grava o resultado e a fonte na planilha.
4. Ao final, cancela os triggers e exibe um alerta de conclusão.

### Colunas escritas na planilha

| Versão | Colunas |
|---|---|
| `Buscador+serper.dev` | (ver constantes `COL_*` no início do arquivo) |
| `API Gemini + Serper.dev` | H: Preço Médio · I: Mediana · J: Preço Máximo · K: Qtd. Preços Encontrados · L: Fonte |
| `PERPLEXITY_API` | M: Preço Médio · N: Mediana · O: Preço Máximo · P: Qtd. Preços Encontrados · Q: Fonte |

### Funções utilitárias (presentes nas versões com Serper/Gemini)

- `filtrarOutliers(precos)` — remove valores fora do intervalo `[Q1 - 1.5×IQR, Q3 + 1.5×IQR]` quando há 3 ou mais preços.
- `valorValido(valor)` — descarta valores não numéricos ou fora da faixa `R$ 1,00 – R$ 500.000,00`.
- `calcularMedia` / `calcularMediana` — estatísticas básicas sobre os preços filtrados.
- `cancelarPesquisa()` / `limparTudo()` / `reprocessarLinha()` — funções de manutenção para pausar, limpar resultados ou reprocessar uma linha específica.

## Passo a passo de instalação

### 1. Estrutura da planilha
Na sua planilha do Google Sheets:
- **Coluna A**: nome dos produtos (obrigatório), a partir da linha 2.

### 2. Abrir o editor de script
- No menu superior: **Extensões → Apps Script**.

### 3. Adicionar o código
- Apague qualquer código existente no editor.
- Cole o conteúdo de **uma** das três versões (escolha conforme a API que pretende usar).
- Substitua as chaves de API pelos seus valores próprios:
  - `SERPER_API_KEY` (https://serper.dev)
  - `GEMINI_API_KEY` (https://aistudio.google.com)
  - `PERPLEXITY_API_KEY` (https://perplexity.ai/settings/api)

> ⚠️ **Segurança:** as chaves estão como constantes no topo do script (placeholders `"tua chave"`). Não publique nem compartilhe o projeto com as chaves reais preenchidas — prefira usar `PropertiesService.getScriptProperties()` para armazenar segredos fora do código-fonte.

### 4. Salvar o projeto
- Clique em **Salvar** e dê um nome ao projeto.

### 5. Primeira execução
- No topo do editor, selecione a função **`iniciarPesquisa`** e clique em **Executar**.
- Conceda as permissões solicitadas pelo Google (Revisar permissões → escolher conta → Avançado → Permitir).

### 6. Execução pela planilha
- Volte para a planilha e rode novamente `iniciarPesquisa` pelo Apps Script.
- Confirme o alerta de início ("Sim").
- O script roda em ciclos automáticos (via triggers temporizados) até processar todas as linhas.

## Limitações e próximos passos

- Ainda em avaliação qual combinação de API/IA entrega o melhor custo-benefício — por isso existem 3 versões em paralelo.
- Funcionalidades planejadas: busca por região e curadoria de lojas favoritas.
- Objetivo: manter a ferramenta simples o suficiente para uso direto pela equipe na planilha.

Sugestões de melhoria são bem-vindas.
