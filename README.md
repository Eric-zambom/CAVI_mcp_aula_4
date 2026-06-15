# 🎓 Servidor MCP: FAQ Inteligente para Secretaria Universitária

Este projeto implementa um **Servidor Model Context Protocol (MCP)** projetado para rodar no **Google Colab**. Ele utiliza a biblioteca `FastMCP` e a API da **Groq** para criar um agente de IA capaz de automatizar o atendimento a alunos, respondendo e-mails e resolvendo dúvidas repetitivas de forma autônoma.

O cenário base é uma secretaria acadêmica que recebe mais de 200 e-mails por dia. O agente usa ferramentas (Tools) para consultar bancos de dados simulados e responder de maneira precisa e contextualizada.

---

## 🚀 Pré-requisitos

Para executar este código, você precisará de:
1. Uma conta no Google (para acessar o [Google Colab](https://colab.research.google.com/)).
2. Uma chave de API gratuita da **Groq** (obtenha em [console.groq.com](https://console.groq.com/)).

---

## ⚙️ Configuração do Ambiente (Google Colab)

Antes de rodar o código, configure a sua chave de API de forma segura no Colab:

1. Abra o painel esquerdo do Google Colab.
2. Clique no ícone de **Chave (Secrets/Segredos)**.
3. Crie um novo *secret* com o nome exato: `GROQ_API_KEY`.
4. Cole a sua chave da Groq no campo "Valor".
5. Ative a chave de seleção (toggle) para conceder acesso ao notebook atual.

---

## 🛠️ Ferramentas (Tools) e Recursos (Resources)

O servidor expõe as seguintes capacidades via MCP para o agente de IA:

### Tools (Ações)
* `buscar_faq(pergunta)`: Varre o banco de dados de Perguntas Frequentes (matrícula, boletos, biblioteca, DP) e retorna a política oficial da universidade.
* `consultar_calendario(curso)`: Recebe o nome do curso do aluno e retorna as datas específicas para matrícula, semanas de prova e entrega de TCC.
* `verificar_documento(tipo, ra)`: Checa o status de validação de um documento específico (ex: histórico, identidade) associado ao Registro Acadêmico (RA) do aluno.

### Resources (Dados Estáticos)
* `faq://todas`: Um endpoint de recurso que retorna um JSON com a lista completa e estruturada de todas as perguntas e respostas frequentes registradas no sistema.

---

## 🏗️ Arquitetura do Código

O código está estruturado em quatro blocos principais (projetados para serem células do Colab):

1.  **Instalação de Dependências:** Instala o `mcp` (que contém o FastMCP) e o SDK oficial do `groq`.
2.  **Dados Simulados e Secrets:** Resgata a chave da Groq com segurança via `google.colab.userdata` e define os dicionários de Python que atuam como nosso banco de dados.
3.  **Configuração do Servidor MCP:** Cria a instância do `FastMCP("FAQ_Universidade")` e utiliza os decoradores `@mcp_server.tool()` e `@mcp_server.resource()` para registrar as funções do sistema.
4.  **Agente Groq (Integração):** * Define as descrições das ferramentas no formato JSON-Schema esperado pelo LLM.
    * Recebe o "e-mail" do aluno.
    * Faz uma chamada à Groq, que decide qual função MCP chamar (*Tool Calling*).
    * Executa a função localmente.
    * Devolve o resultado para o LLM gerar a resposta final amigável ao aluno.

---

## 🧪 Testes no Google Colab

Devido às restrições de rede e portas fechadas do ambiente em nuvem do Google Colab, testar o servidor diretamente com o **MCP Inspector UI** requer o uso de túneis de rede (como o *localtunnel*). 

Para contornar isso e focar no comportamento da IA, a **Célula 4** do código implementa um teste programático *End-to-End*. Ela simula o recebimento dos e-mails e imprime a cadeia de pensamento:
1. Recebimento da mensagem.
2. Acionamento da *Tool* adequada pelo agente.
3. Resposta final amigável.

**Exemplos de prompts testados:**
* *"Quais são as datas de prova para Engenharia Civil?"*
* *"Meu RA é 2026001, minha identidade já foi aprovada?"*
* *"Como faço para pagar o boleto da mensalidade?"*

---

## 🤝 Possíveis Melhorias Futuras
* Substituir os dicionários simulados por conexões a bancos de dados reais (SQL/NoSQL) ou planilhas do Google Sheets.
* Conectar o *input* e *output* do agente diretamente a uma API de e-mail (como Gmail API ou SendGrid) para responder automaticamente.
