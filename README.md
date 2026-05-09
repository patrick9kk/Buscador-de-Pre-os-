# Buscador-de-Preços
Este projeto é um script automatizado para Google Sheets (Apps Script) que realiza a coleta e análise de preços de produtos na internet de forma inteligente e escalável.

A partir de uma lista de produtos, o sistema busca preços utilizando a API do Google (via Serper) e, quando necessário, utiliza inteligência artificial (Gemini) para extrair valores diretamente de resultados orgânicos. Os dados coletados são processados, filtrados e organizados automaticamente na planilha.

# Futuras atualizações
Ainda estou avaliando qual método e API entregam o melhor custo-benefício, por isso desenvolvi uma versão para cada cenário. Meu objetivo é manter a ferramenta intuitiva para que todos da equipe consigam utilizá-la diretamente na planilha. Assim que validar a precisão dos preços, seguirei com a compra dos tokens e implementarei novas funcionalidades, como busca por região e curadoria de lojas favoritas. Sugestões de melhorias são muito bem-vindas!

# Passo a passo para instalação

- Estrutura da Planilha
Na sua planilha:
Coluna A: Nome dos produtos (obrigatório)

# Criar a planilha
- Acesse o Google Sheets
- Crie uma nova planilha
- Insira os nomes dos produtos na coluna A, a partir da linha 2

# 2. Abrir o editor de script
- No menu superior, clique em:
- Extensões > Apps Script
- Uma nova aba será aberta
  
# 3. Adicionar o código
- Apague qualquer código existente
- Cole o código completo do projeto
- Localize a linha: const PERPLEXITY_API_KEY = "SUA_CHAVE_AQUI"; ou "sua chave"
- Substitua pela sua chave de API

# 4. Salvar o projeto
- Clique em "Salvar"
- Dê um nome ao projeto
  
# 5. Executar o script pela primeira vez
- No topo do editor, selecione a função:
- iniciarPesquisa
- Clique em "Executar"
  
- O Google pedirá permissões:
- Clique em "Revisar permissões"
- Escolha sua conta
- Clique em "Avançado" (se aparecer)
- Clique em "Permitir"
  
# 6. Executar pela planilha
- Volte para a planilha
- Execute novamente a função iniciarPesquisa pelo Apps Script
- Um alerta aparecerá confirmando o início
- Clique em "Sim"
