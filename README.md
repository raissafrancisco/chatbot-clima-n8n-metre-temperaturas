🌤️**Projeto: O Mestre das Temperaturas**
Este projeto consiste numa automação avançada desenvolvida no n8n (Versão 2.3.6) que integra a API de clima OpenWeather com a inteligência artificial Google Gemini. O bot recebe o nome de uma cidade via Telegram e responde com a temperatura atual num tom místico e personalizado.

🎭 **Identidade do Bot**

O bot possui uma personalidade única projetada para transformar uma simples consulta climática numa experiência imersiva.

- Nome do Bot: Mestre das Temperaturas.

- Papel: Um sábio guardião de oráculos climáticos que decifra os mistérios do tempo para os viajantes.

- Tom de Voz: Místico, respeitoso e enigmático, tratando o utilizador como "jovem aprendiz" ou "viajante".

- Uso de Emojis: Emprega ícones como ⚡, 🔮, 📜 e ✨ para reforçar a atmosfera de mistério.

**Configuração Sugerida para o Telegram**

**Botpic (Avatar):**

<p align="center">
<img src="avatar-mestre.png.png" alt="Avatar do Mestre das Temperaturas" width="300">
</p>

- Description: Bem-vindo ao santuário do clima! 🔮 Sou o Mestre das temperaturas e recebi o dom de consultar os céus do Brasil pra te ajudar a navegar as 4 estações. Digite o nome da sua cidade e estado e eu revelarei a verdade térmica agora mesmo! 🌡️⚡️

- About (Bio): O oráculo definitivo para quem não aguenta mais ser pega de surpresa pelo clima bipolar do Brasil. ☀️⛈️

- Um plus: Link de Acesso ao meu bot t.me/mestre_temperaturas_br_bot (deixei meu fluxo ativo, mas pode ter períodos de indisponibilidade, pois uso o n8n na máquina local através do docker)

🚀 **Arquitetura e Resiliência (Fallback)**

O workflow foi desenhado para ser blindado contra falhas:

- Fallback Determinístico: Se a API de IA falhar, um nó Code (JavaScript) detecta o erro e ativa uma resposta fixa baseada nos dados reais da OpenWeather.

- Infraestrutura: O projeto inclui um arquivo docker-compose.yml para facilitar o deploy do n8n em ambientes containerizados.

🔍**Anatomia do Fluxo (Descrição dos Nós)**

Seguindo os requisitos obrigatórios, o fluxo é composto pelos seguintes passos:

- Trigger telegram message (Telegram Trigger node): Configurado para receber mensagens de texto.

- Captura e formatação (Set node): Captura o texto recebido e o insere na variável queue. O texto é normalizado (remoção de espaços, letras minúsculas e ajuste de acentuação).

- Chamada à OpenWeather (HTTP Request): Consulta o endpoint oficial usando a opção “Send Query Parameters”:

  - q: recebe a variável queue.

  - units: metric (Celsius).

  - lang: pt_br.

  - appid: OPENWEATHER_API_KEY.

- Validar Resposta e tratar erros (IF node): Verifica se a cidade existe e se o status é de sucesso. Caso contrário, desvia para a mensagem de erro.

- Formatar dados temperatura encontrada (Set node): Arredonda a temperatura e prepara a string com cidade e temperatura, além da mensagem de fallback.

- Message a model (Gemini): Gera a resposta mística via IA.

- Code in JavaScript: Nó de inteligência que decide entre o sucesso da IA ou ativa o fallback determinístico.

- Telegram responder (Telegram Send Message node): Envia a resposta final ou a mensagem de erro (❌ Cidade não encontrada. Use o formato Cidade,UF).

🛠️ **Passos para Importação e Configuração**

**1. Importar o Workflow**

Utilize o arquivo: workflow-chatbot-telegram.json.

Via Interface: No n8n, use a opção Import from File e selecione o arquivo json. Ou use ctrl A + ctrl C para copiar todo o conteúdo do json e ctrl v para colar na janela do n8n.

**2. Configuração via Docker (Opcional)**

Se optar por rodar o ambiente localmente via Docker:
Utilize o arquivo docker-compose.yml fornecido.

⚠️ IMPORTANTE: Por questões de segurança, as credenciais no arquivo estão marcadas como INSIRA_SUA_CHAVE_AQUI. É necessário editar o arquivo e inserir os seus próprios dados (tokens de banco de dados, chaves de criptografia, domínio e tokens do ngrok) antes de subir o conteúdo com docker-compose up -d.

**3. Configurar Credenciais no n8n**

Após subir o worflow no n8n, configure os seguintes Tokens/ Keys na interface dos nós:

- TELEGRAM_BOT_TOKEN: Configure a credencial do Telegram API, entre no nó trigger do telegram, em "Credential to connect with" e clique em "create new credential" ou selecione uma existente, IMPORTANTE que a credencial contenha o token do bot gerado no botfather do telegram. Lembre-se de selecionar a mesma credencial do trigger nos nós de resposta "send message" do telegram. 

- **OpenWeather (Query Auth): No nó "Chamada à OpenWeather", a autenticação deve estar definida como Query Auth.Crie uma nova credencial. O nome do parâmetro deve ser appid e o valor deve ser a sua chave de API.**

- Google Gemini API: Configure a chave no nó Gemini em "Credential to connect with" e clique em "create new credential" ou selecione uma existente. Além disso revise se a configuração do nó se manteve como no json importado: dentro do nó em setting > On Error > a opção Continue (using error output). 

⚙️ **Como Executar o Chatbot**

- Publicar: Clique no botão Publish para ativar o fluxo.
- Enviar Mensagem: Envie o nome de uma cidade e estado (Ex: Rio de Janeiro,RJ) para o seu bot no Telegram.
O que esperar: O bot responderá com a previsão do tempo no tom místico do Mestre ou solicitará o formato correto em caso de erro.

🧪 **Teste de Fallback (Requisito de Avaliação)**

Para validar a resiliência: insira uma chave inválida propositalmente no nó do Gemini. O nó Code garantirá a resposta utilizando a lógica de contingência programada com a mensagem determinística, utilziando os dados reais de temperatura da open weather tratados anteriormente.






