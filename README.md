<html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Projeto DMCC</title>
    <style>
        .container{   
            background: #006494;
            background-repeat: no-repeat;
            max-height:650px;
            background-size: cover;
            margin-bottom: 150px;
            margin:0;
        }
        .box{
            background: #f0f0f0;
            box-shadow: 10px 10px 20px #1B98E0;
            border-radius: 20px;
            justify-content: center;
            align-items: center;
            align-content: center;
            justify-items: center;
            flex-direction: column;
            margin: 500px;
            margin-top: 100px;
            margin-bottom: 150px;
          }
        .reiniciar{
            background-color:#1B98E0 ;
            width: 100px;
            height: 50px;
            color: white;
            font-size: large;
            font-family: serif;
            border-radius: 15px;
            border-color: #1B98E0 ;
            margin: 10px;
            box-shadow: 2px 2px 5px #13293D;
        }
        h1{
            color: #13293D;
            font-size: 2.5rem;
            font-family: ;
            margin-bottom: 30px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
            margin: 10px;
        }
        .botao{
            width: 70px;
            height: 70px;
            border-radius: 10px;
            border-color: #006494;
            box-shadow: 2px 2px 5px #13293D;
            margin: 3px;   
        }
        .pvp{
            background-color: #1B98E0;
            width: 100px;
            height: 50px;
            margin: 10px;
            border-radius: 10px;
            border-color: #1B98E0;
            color: #f0f0f0;
            box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
        }
        .pve{
            background-color: #1B98E0;
            width: 100px;
            height: 50px;
            margin: 10px;
            border-radius: 10px;
            border-color: #1B98E0;
            color: #f0f0f0;
            box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.1);
        }
        section {
            display: flex;
            gap: 5px;
            justify-content: center;
            margin-bottom: 5px; 
          }
    </style>
</head>
<body>
    <div class="container">
      <br>
      <br>
       <div class="box">
            <h1>Jogo Da Velha</h1>
            <section>
                <button class="pvp" id="pvp">Jogador X Jogador</button>
                <button class="pve" id="pve">Jogador X Computador</button>
            </section>
            <section>
                <button class="botao "data-i="1"></button> 
                <button class="botao "data-i="2"></button>
                <button class="botao "data-i="3"></button>
            </section>
            <section>
                <button class="botao "data-i="4"></button> 
                <button class="botao "data-i="5"></button>
                <button class="botao "data-i="6"></button>
            </section>
            <section>
                <button class="botao "data-i="7"></button> 
                <button class="botao "data-i="8"></button>
                <button class="botao "data-i="9"></button>
            </section>
            <button class="reiniciar" id="restart">Reiniciar</button>
        </div>
        <br>
        <br>
      </div>
  <script>
    let modo = "pvp";
    let jogadorAtual = "X";
    let tabuleiro = Array(9).fill("");
    const botoes = document.querySelectorAll(".botao");
    const btnPvp = document.getElementById("pvp");
    const btnPve = document.getElementById("pve");
    const btnRestart = document.getElementById("restart");
    btnPvp.addEventListener("click", () => {
      modo = "pvp";
      reiniciarJogo();
    });
    btnPve.addEventListener("click", () => {
      modo = "bot";
      reiniciarJogo();
    });
    btnRestart.addEventListener("click", reiniciarJogo);
    botoes.forEach((botao) => {
      botao.addEventListener("click", () => {
        const i = parseInt(botao.dataset.i) - 1;
        if (tabuleiro[i] !== "") return;
        fazerJogada(i, jogadorAtual);
        if (verificarFim(jogadorAtual)) return;
        jogadorAtual = jogadorAtual === "X" ? "O" : "X";
        if (modo === "bot" && jogadorAtual === "O") {
          setTimeout(jogadaBot, 500);
        }
      });
    });
    function fazerJogada(i, jogador) {
      tabuleiro[i] = jogador;
      botoes[i].textContent = jogador;
    }
    function jogadaBot() {
      const vitorias = [
        [0,1,2], [3,4,5], [6,7,8],
        [0,3,6], [1,4,7], [2,5,8],
        [0,4,8], [2,4,6]
      ];
      // 1. Tentar vencer
      for (let grupo of vitorias) {
        const [a, b, c] = grupo;
        const valores = [tabuleiro[a], tabuleiro[b], tabuleiro[c]];
        if (valores.filter(v => v === "O").length === 2 && valores.includes("")) {
          const i = grupo[valores.indexOf("")];
          fazerJogada(i, "O");
          verificarFim("O");
          jogadorAtual = "X";
          return;
        }
      } // 2. Bloquear jogador
      for (let grupo of vitorias) {
        const [a, b, c] = grupo;
        const valores = [tabuleiro[a], tabuleiro[b], tabuleiro[c]];
        if (valores.filter(v => v === "X").length === 2 && valores.includes("")) {
          const i = grupo[valores.indexOf("")];
          fazerJogada(i, "O");
          verificarFim("O");
          jogadorAtual = "X";
          return;
        }
      }
      // 3. Centro
      if (tabuleiro[4] === "") {
        fazerJogada(4, "O");
        verificarFim("O");
        jogadorAtual = "X";
        return;
      }
      // 4. Canto
      const cantos = [0, 2, 6, 8].filter(i => tabuleiro[i] === "");
      if (cantos.length > 0) {
        const i = cantos[Math.floor(Math.random() * cantos.length)];
        fazerJogada(i, "O");
        verificarFim("O");
        jogadorAtual = "X";
        return;
      }
      // 5. Aleatório
      const livres = tabuleiro
        .map((v, i) => (v === "" ? i : null))
        .filter(i => i !== null);
      if (livres.length > 0) {
        const i = livres[Math.floor(Math.random() * livres.length)];
        fazerJogada(i, "O");
        verificarFim("O");
        jogadorAtual = "X";
      }
    }
    function verificarFim(jogador) {
      if (verificarVitoria(jogador)) {
        setTimeout(() => alert(jogador === "O" ? "Computador venceu!" : `Jogador ${jogador} venceu!`), 100);
        return true;
      }
      if (tabuleiro.every(cell => cell !== "")) {
        setTimeout(() => alert("Empate!"), 100);
        return true;
      }
      return false;
    }
    function verificarVitoria(jogador) {
      const vitorias = [
        [0,1,2], [3,4,5], [6,7,8],
        [0,3,6], [1,4,7], [2,5,8],
        [0,4,8], [2,4,6]
      ];
      return vitorias.some(grupo => grupo.every(i => tabuleiro[i] === jogador));
    }
    function reiniciarJogo() {
      tabuleiro = Array(9).fill("");
      jogadorAtual = "X";
      botoes.forEach(botao => botao.textContent = "");
    }
  </script>
</body>
</html>
