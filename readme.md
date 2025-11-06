// Importando o módulo 'express' e atribuindo-o à constante 'app'
const app = require('express')();
// Importando o módulo 'http' e criando um servidor com ele, atribuindo-o à constante 'http'
const http = require('http').createServer(app);
// Importando o módulo 'socket.io' e passando o servidor 'http' como parâmetro, atribuindo-o à constante 'io'
const io = require('socket.io')(http);

// Rota para a página inicial
app.get('/', (req, res) => res.sendFile(__dirname + '/index.html'));

// Evento para quando o cliente se conecta ao servidor via Socket.io
io.on('connection', (socket) => {
  console.log('Usuário conectado');

  // Evento para quando o cliente envia uma mensagem via Socket.io
  socket.on('chat message', (data) => io.emit('chat message', data));

  // Evento para quando o cliente se desconecta do servidor via Socket.io
  socket.on('disconnect', () => console.log('Usuário desconectado'));
});

// Inicia o servidor na porta 3000
http.listen(3000, () => {
  console.log(`Servidor rodando na porta 3000 - Link http://localhost:3000`);
});

<!-- index -->

<!DOCTYPE html>
<html lang="pt-br">
  <head>
    <title>Bate-papo Node.js e Socket.IO</title>
  </head>
  <body>
    <ul id="mensagens"></ul>
    <form>
      <input id="nome" placeholder="Seu nome de usuário" autocomplete="off" /><br>
      <input id="mensagem" placeholder="Sua mensagem" autocomplete="off" /><button>Enviar</button>
    </form>
    <!-- Importa o script do Socket.IO -->
    <script src="/socket.io/socket.io.js"></script>
    <script>
      // Cria uma instância do Socket.IO
      const socket = io();
      // Seleciona o input do nome do usuário
      const nomeInput = document.getElementById('nome');
      // Seleciona o input da mensagem
      const mensagemInput = document.getElementById('mensagem');
      // Seleciona a lista de mensagens
      const mensagens = document.getElementById('mensagens');

      // Adiciona um evento de escuta para o envio do formulário
      document.querySelector('form').addEventListener('submit', event => {
        // Previne o envio padrão do formulário
        event.preventDefault();
        // Obtém o valor do input do nome do usuário
        const nome = nomeInput.value;
        // Obtém o valor do input da mensagem
        const mensagem = mensagemInput.value;
        // Verifica se ambos os campos foram preenchidos antes de enviar a mensagem
        // Verifica se os valores são válidos (não estão em branco)
        //trim() é um método da linguagem JavaScript que remove os espaços em branco do início e do final de uma string.
        // emit envia um evento chamado "chat message" com um objeto contendo os valores de nome e mensagem para o servidor.
        nome.trim() && mensagem.trim() && socket.emit('chat message', { nome, mensagem });
        // Limpa o input da mensagem
        mensagemInput.value = '';
        // Desabilita o input do nome do usuário após a primeira mensagem
        nomeInput.disabled = true; 
      });

      // Adiciona um evento de escuta para o evento de mensagem recebido do servidor
      socket.on('chat message', dados => {
        // Cria um elemento de lista para exibir a mensagem
        const lista = document.createElement('li');
        // Define o texto da mensagem
        lista.textContent = `${dados.nome}: ${dados.mensagem}`;
        // Adiciona o elemento de lista à lista de mensagens
        mensagens.appendChild(lista);
      });
    </script>
  </body>
</html>
