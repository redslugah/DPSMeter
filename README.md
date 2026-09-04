# Huntera Party Analyzer

Userscript para Tampermonkey que acompanha informacoes uteis da party durante uma hunt no Huntera.

O objetivo e oferecer uma visao compartilhada do desempenho da PT, sem depender apenas das informacoes individuais exibidas no jogo. Cada jogador instala o mesmo script, entra na mesma party usando um nome e uma senha, e os dados aparecem no painel de todos os participantes.

## O que o script acompanha

Para cada personagem da party, o painel mostra:

- dano total;
- ranking de dano;
- DPS principal;
- DPS dos ultimos 10 segundos;
- maior hit;
- participacao percentual no dano total;
- XP acumulado;
- XP por hora.

O painel tambem informa o tempo ativo da hunt e se o combate esta ativo ou pausado.

O limite atual e de quatro personagens por party.

## Por que existe

Em uma hunt, a party precisa de informacoes rapidas para entender o ritmo do combate, comparar o dano dos personagens e acompanhar a experiencia obtida. O jogo fornece as mensagens no chat de combate, mas nao apresenta uma visao compartilhada e resumida para toda a PT.

O Huntera Party Analyzer observa as novas mensagens do log de combate, identifica dano e XP, e envia somente esses eventos para um Web Service. O servidor organiza os dados por party e devolve o estado atualizado para os jogadores conectados.

## Como funciona o compartilhamento

O projeto usa um Web Service publico com autenticacao por party:

```text
https://hunterapartyanalyzer.onrender.com
```

Qualquer pessoa pode usar esse servidor, mas os dados nao ficam todos misturados. Cada party possui:

- nome proprio;
- senha propria;
- token de acesso aleatorio;
- estado separado das outras parties.

A senha e usada para criar ou conectar na party. Depois da autenticacao, o script usa o token da party nas requisicoes protegidas. O token fica salvo localmente no navegador.

## Requisitos

- Google Chrome ou Opera GX;
- extensao Tampermonkey;
- acesso ao `huntera.com.br`;
- uma party criada no sistema ou as credenciais de uma party existente.

Nao e necessario instalar Python, executar um servidor local ou fazer deploy para usar o Web Service publico.

## Instalacao

1. Instale a extensao [Tampermonkey](https://www.tampermonkey.net/) no navegador.
2. Abra o painel do Tampermonkey.
3. Crie um novo userscript.
4. Apague o conteudo inicial criado pela extensao.
5. Copie todo o conteudo de [`Script.js`](Script.js) deste repositorio.
6. Cole o conteudo no editor do Tampermonkey.
7. Salve o script.
8. Abra ou recarregue uma pagina do jogo em `https://huntera.com.br/game*`.

O `Script.js` ja esta configurado para usar o Web Service publico. Nao altere estas linhas se quiser usar o servidor compartilhado do projeto:

```javascript
// @connect      hunterapartyanalyzer.onrender.com
var API = "https://hunterapartyanalyzer.onrender.com";
```

O token de acesso da party e armazenado pelo Tampermonkey usando `GM_setValue`. Instalacoes antigas que ainda tenham o token no `localStorage` fazem uma migracao automatica na primeira abertura.

## Primeira configuracao

Ao abrir o jogo pela primeira vez, o painel pedira os dados da party.

### Criar uma nova party

1. Informe um nome para a PT.
2. Informe uma senha com pelo menos quatro caracteres.
3. Clique em **Criar nova PT**.
4. Compartilhe com os outros jogadores o nome exato da PT e a senha.

### Entrar em uma party existente

1. Informe o nome da PT.
2. Informe a senha recebida do lider ou dos outros jogadores.
3. Clique em **Conectar a PT**.

A party nao precisa ser criada novamente em cada navegador. Um jogador cria a party e os demais usam **Conectar a PT** com as mesmas credenciais.

### Configurar o personagem

Depois de entrar na party:

1. Informe o nome do personagem.
2. Selecione a vocacao.
3. Clique em **Salvar**.

As vocacoes disponiveis sao Knight, Druid, Sorcerer e Paladin.

Tambem e possivel escolher **So visualizar**. Esse modo mostra os dados da party sem registrar a aba como um dos quatro personagens.

## Durante a hunt

- O script acompanha somente novas linhas adicionadas ao log de combate.
- Recarregar a pagina nao reprocessa linhas antigas do log.
- O dano e o XP continuam associados a party no servidor.
- O botao de reset zera os dados da hunt atual, mas preserva os personagens cadastrados.
- O botao de party permite trocar de party ou criar outra.
- O botao de renomear permite alterar o personagem e a vocacao da aba atual.
- O botao de exportacao copia um JSON com os dados atuais da party e de todos os personagens para o clipboard.
- O painel pode ser arrastado e possui um modo grande para facilitar a visualizacao.
- A visao principal mostra o nome/vocacao, dano, DPS e XP/h de cada personagem, alem do dano e XP totais da party.
- Clique em um personagem para abrir os detalhes completos, incluindo maior hit e DPS dos ultimos 10 segundos. Use **Voltar para a PT** para retornar ao ranking.

O script reconhece mensagens de dano em portugues e ingles:

```text
Voce acertou <alvo> causando 1.234.
You hit <target> for 1.234.
```

Tambem reconhece mensagens de XP:

```text
Voce ganhou 73 de experiencia.
You gained 73 experience points.
```

## Tempo de combate e XP/h

O servidor considera o combate ativo enquanto houve dano recente. O valor padrao e de 15 segundos sem dano.

O calculo de XP/h usa um tempo ativo acumulado separado, tambem com padrao de 15 segundos sem XP. O acumulador continua considerando a hunt inteira mesmo quando os eventos antigos saem da janela de retencao, evitando que sessoes longas produzam um XP/h artificialmente alto.

## Limites e observacoes

- Cada party aceita ate quatro personagens registrados.
- O servidor atual mantem parties, personagens e eventos em memoria.
- Um reinicio, novo deploy ou periodo de inatividade do Render pode apagar os dados das parties.
- O plano gratuito do Render pode levar alguns segundos para acordar. Na primeira tentativa, aguarde e tente novamente se aparecer timeout.
- Parties sem atividade por 4 dias sao removidas automaticamente. Isso tambem invalida o token correspondente.
- O servidor limita a quantidade de parties e de eventos mantidos na janela de 15 minutos. Em uma hunt normal, esses limites nao alteram o calculo; em caso de excesso anormal, novos eventos podem ser recusados temporariamente com status `429`.
- A party e compartilhada por nome e senha. Use uma senha que voce possa distribuir apenas para os jogadores da sua PT.
- O script envia eventos de dano e XP, mas nao envia a senha da party nas requisicoes de estado, dano ou experiencia.

## Problemas comuns

### O painel mostra `PT NAO CONECTADA`

Abra o botao de party e conecte novamente usando o nome e a senha corretos.

### O painel mostra `OFFLINE` ou erro de API

Confira se o script instalado e o arquivo atual deste repositorio. No Tampermonkey, confirme que o script esta habilitado e que o dominio `hunterapartyanalyzer.onrender.com` esta autorizado no `@connect`.

Tambem pode ser apenas o tempo de inicializacao do Render. Aguarde alguns segundos e recarregue o jogo.

### A party esta cheia

O limite e de quatro personagens. Para acompanhar sem ocupar uma vaga, use o modo **So visualizar**.

### O dano nao aparece

Confirme que:

- a pagina e uma pagina do jogo Huntera;
- o personagem esta registrado na party;
- o nome e a vocacao foram salvos;
- o log de combate esta visivel e recebendo novas mensagens;
- o script esta habilitado no Tampermonkey.

## Desenvolvimento local e servidor proprio

O repositorio tambem contem `huntera_party_analyzer_server.py`, um servidor Python sem dependencias externas. Para executar localmente:

```powershell
python huntera_party_analyzer_server.py
```

Nesse caso, o servidor usa a porta definida por `PORT` ou `8765` por padrao. Para alterar os tempos no servidor, podem ser usadas:

```text
COMBAT_TIMEOUT=15
XP_TIMEOUT=15
```

Para o uso normal, contudo, basta instalar o `Script.js` e utilizar o Web Service publico informado acima.

## Licenca

Este projeto e disponibilizado para uso pessoal e colaborativo nas hunts do Huntera.
