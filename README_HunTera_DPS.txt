HUNTERA PARTY ANALYZER 3.0 - CHROME + OPERA GX

Arquivos:
- HunTera_DPS_Counter.user.js -> instalar no Tampermonkey em Chrome e Opera GX.
- huntera_party_analyzer_server.py -> servidor que compartilha o DPS entre ate 4 pessoas.

DEPLOY NO RENDER
1. Suba estes arquivos em um repositorio GitHub.
2. No Render, crie um Web Service conectado ao repositorio.
3. Use Runtime Python e o comando de start: `python huntera_party_analyzer_server.py`.
4. O Render fornece uma URL parecida com `https://meu-dps.onrender.com`.
5. No `Script.js`, troque `https://SEU-APP.onrender.com` pela URL real do seu servico.
6. Reinstale/atualize o userscript no Tampermonkey de todos os jogadores.

AJUSTAR O TEMPO DE COMBATE
O tempo sem dano antes de pausar o combate e configurado no Render:
1. Abra o Web Service e entre em `Environment`.
2. Adicione a variavel `COMBAT_TIMEOUT` com o valor em segundos.
3. Salve e faca um novo deploy.

Exemplos: `15` mantem o combate ativo por 15 segundos sem hit; `20` ou `30`
podem ser usados se a party fizer pausas maiores. O padrao do servidor e 15.

XP POR HORA
O script reconhece automaticamente `Você ganhou 73 de experiência.` e
`You gained 73 experience points.`. O painel mostra o XP acumulado e o XP/h
de cada personagem.

O tempo ativo usado no XP/h e independente do DPS e acumulado durante toda a
hunt. Para alterar, adicione no Render a variavel `XP_TIMEOUT`, tambem em segundos, e faca um novo deploy.
Se nao for configurada, o padrao e 15 segundos.

O servidor usa automaticamente a porta `PORT` fornecida pelo Render. O endpoint
`/health` pode ser usado para verificar se ele esta funcionando.

COMO USAR
1. Instale Tampermonkey no Chrome e no Opera GX.
2. No Tampermonkey de CADA navegador, crie um script novo e cole HunTera_DPS_Counter.user.js.
3. No Windows, abra um Prompt de Comando/PowerShell na pasta dos arquivos e execute (modo local):
   python huntera_party_analyzer_server.py
4. Deixe essa janela aberta enquanto usar o DPS.
5. Abra/recarregue o HunTera nos dois navegadores.
6. No primeiro acesso, clique em `Criar nova PT`, informe o nome e uma senha.
7. Nos outros navegadores, informe os mesmos dados e clique em `Conectar à PT`.
8. Configure o nome e vocação de cada personagem.
9. Clique no botao ↺ uma vez no começo da hunt para zerar a party.

IMPORTANTE
- Cada party tem nome e senha próprios. O servidor entrega um token aleatorio,
   e as chamadas seguintes usam esse token.
- O limite é de 4 personagens por party.
- O token fica armazenado pelo Tampermonkey, separado do localStorage da pagina.
- A senha da party tambem pode ficar armazenada pelo Tampermonkey para permitir reconexao automatica.
- No Render gratuito, os dados ficam em memoria e podem ser perdidos se o servico
   reiniciar ou dormir. O plano gratuito tambem pode demorar alguns segundos para
   acordar no primeiro acesso.
- Parties sem atividade por 4 dias e eventos acima dos limites de protecao podem
   ser removidos ou recusados. Esses limites nao afetam uma hunt normal.
- Se o Render reiniciar e perder o estado em memoria, o script tenta recriar a
   party automaticamente, mas os dados antigos so podem ser preservados com banco persistente externo.
- O script usa GM_xmlhttpRequest para falar com a URL configurada do servidor.
- O script foi ajustado para o log em inglês: "You hit ... for 1234".
- Linhas antigas do combat log NÃO são reprocessadas ao recarregar a página, evitando duplicação.
- Reset zera a hunt, mas preserva os personagens cadastrados.
- DPS principal = dano total / duração total da hunt desde o primeiro hit.
- DPS 10s = dano causado nos últimos 10 segundos / 10.
- Combate fica como ativo enquanto houver pelo menos um hit da party dentro do
   valor configurado em `COMBAT_TIMEOUT`.
- Pausas maiores que esse valor não entram no tempo ativo.
- "Pausado" não apaga o dano; apenas indica que não houve hit recente.

SE python NAO EXISTIR
Instale Python 3 e marque a opcao "Add Python to PATH" durante a instalação.

SE O WINDOWS PERGUNTAR SOBRE FIREWALL
No modo local, o programa usa 127.0.0.1 (localhost). No Render, nao e necessario
abrir portas no Windows.
