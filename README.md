# RetroX | Overlay para OBS (League of Legends)

Pacote de overlays animados em **1920×1080** para transmissões no **OBS Studio**, feitos em HTML, CSS e JavaScript. Funciona como **Fonte Navegador** e não precisa instalar plugin.

São **3 versões visuais** do mesmo overlay. Escolha uma:

| Versão | Arquivo | Como é | Peso para o PC |
|---|---|---|---|
| **Imagens** | `overlay-retrox.html` | Intro e Final usam as artes geradas por IA (Ahri). Live e BRB usam a moldura neon. | Mais leve |
| **Só código** | `overlay-retrox-codigo.html` | Tudo desenhado em código: anéis hextech, partículas, feixes de luz. Sem arte de personagem. | Médio |
| **Híbrida** | `overlay-retrox-hibrido.html` | Intro e Final com as artes **animadas** (zoom, brilho do orbe, partículas, anel na contagem). Live, Game e BRB em código. | Mais pesado |

> Todas têm as mesmas **5 cenas**: `intro`, `live`, `game`, `end` e `brb`, e os mesmos recursos (chat, alertas, meta, último inscrito).

---

## 1. O que tem no pacote

```
retrox/
├── overlay-retrox.html          (versão Imagens)
├── overlay-retrox-codigo.html   (versão Só código)
├── overlay-retrox-hibrido.html  (versão Híbrida)
├── intro.jpg                    (arte "Começando em breve")
├── final.jpg                    (arte "Obrigado por assistir")
├── neon.png                     (moldura neon com transparência)
├── logo-retrox.png              (logo do canal, fundo transparente)
└── logo-lol.png                 (logo League of Legends, fundo transparente)
```

**Quais arquivos cada versão precisa** (deixe sempre na **mesma pasta** que o HTML):

| Versão | intro.jpg | final.jpg | neon.png | logo-retrox.png | logo-lol.png |
|---|:-:|:-:|:-:|:-:|:-:|
| Imagens | sim | sim | sim | sim | sim |
| Só código | não | não | não | sim | sim |
| Híbrida | sim | sim | não | sim | sim |

Se faltar uma imagem que a versão usa, o espaço dela aparece vazio.

---

## 2. Instalação

1. Baixe o projeto: no GitHub, clique em **Code, Download ZIP** e extraia.
2. Mova a pasta para um lugar **fixo** do computador, por exemplo `C:\retrox\`. Se você mover depois, vai precisar trocar as URLs no OBS.
3. Escolha **uma** das três versões e anote o nome do arquivo HTML.

---

## 3. Montando as cenas no OBS

Crie **5 cenas** no OBS (pode usar outros nomes): Intro, Live, Game, Final e BRB.

Em cada cena, adicione uma fonte **Navegador** (botão `+` em Fontes, depois **Navegador**) com estas configurações:

- **Desmarque "Arquivo local".** Se ficar marcado, o `?scene=` é ignorado e todas as cenas mostram a Intro.
- **Largura:** `1920`
- **Altura:** `1080`
- **URL:** veja a tabela abaixo.

Troque `ARQUIVO.html` pelo arquivo da versão escolhida:

| Cena | URL (Windows) |
|---|---|
| Intro | `file:///C:/retrox/ARQUIVO.html?scene=intro` |
| Live | `file:///C:/retrox/ARQUIVO.html?scene=live` |
| Game | `file:///C:/retrox/ARQUIVO.html?scene=game` |
| Final | `file:///C:/retrox/ARQUIVO.html?scene=end` |
| BRB | `file:///C:/retrox/ARQUIVO.html?scene=brb` |

**Exemplo (versão Híbrida, cena Live):**
`file:///C:/retrox/overlay-retrox-hibrido.html?scene=live`

**Dicas sobre o caminho**
- Use barras normais `/`, nunca `\`.
- Espaço no nome da pasta vira `%20` (ex.: `Minha%20Pasta`).
- macOS: `file:///Users/seunome/retrox/ARQUIVO.html?scene=intro`
- Linux: `file:///home/seunome/retrox/ARQUIVO.html?scene=intro`

### Configurações recomendadas em cada fonte Navegador

- **Desligar fonte quando não estiver visível**: marcado
- **Atualizar navegador quando a cena ficar ativa**: marcado (a contagem da Intro volta ao início toda vez)
- **FPS personalizado**: 30
- **Controlar áudio via OBS**: marcado (senão o som dos alertas não vai para a live)

---

## 4. Posições de cada cena

Em **Live** e **Game**, o jogo e a câmera ficam **por baixo** do overlay, e o overlay tem "janelas" transparentes para eles aparecerem. Use exatamente estas posições e tamanhos (menu *Transformar, Editar transformação*):

### Cena Live

| Fonte | Posição X, Y | Tamanho |
|---|---|---|
| Captura do jogo | 26, 26 | 1468 × 826 |
| Câmera | 1526, 26 | 368 × 277 |
| Chat do Restream | 1526, 381 | 368 × 673 |

**Ordem das fontes (de cima para baixo):**
1. Chat do Restream (**por cima de tudo**)
2. Overlay (Fonte Navegador `?scene=live`)
3. Câmera
4. Captura do jogo

### Cena Game (jogo em tela cheia)

| Fonte | Posição X, Y | Tamanho |
|---|---|---|
| Captura do jogo | 0, 0 | 1920 × 1080 |
| Câmera | 28, 818 | 318 × 234 |

**Ordem:** overlay no topo, depois a câmera, depois o jogo.

### Intro, Final e BRB

Só o overlay, sem câmera nem jogo. Se quiser, coloque música de fundo como outra fonte.

---

## 5. Configurando os textos, redes e meta

Abra o arquivo HTML escolhido em um editor de texto (Bloco de Notas funciona) e procure o bloco `CFG` perto do final:

```js
const CFG={
  brbMsg:"Pausa rápida. Já já a gente volta!",
  lastFollower:"—",
  socials:["YouTube: /@retrox","TikTok: @retrox","Instagram: @retrox","Discord: /retrox"],
  countdownMin:5,
  goal:{label:"META DE INSCRITOS",current:0,target:100},
  seJWT:"",
  volume:0.12
};
```

| Campo | Para que serve |
|---|---|
| `brbMsg` | Mensagem da tela "Já volto" |
| `lastFollower` | Texto inicial do "último inscrito" |
| `socials` | Lista das redes (até 4 cabem bem). **Troque pelos @ reais** |
| `countdownMin` | Minutos da contagem regressiva da Intro |
| `goal` | Nome da meta, valor atual e valor alvo |
| `seJWT` | Token do StreamElements para alertas reais (veja seção 7) |
| `volume` | Volume dos sons dos alertas (0 a 1) |

Salve o arquivo e, no OBS, clique com o botão direito na fonte e escolha **Atualizar cache do navegador atual**.

> A contagem da Intro e a meta voltam ao valor do `CFG` sempre que a página recarrega.

---

## 6. Chat ao vivo (Restream)

O chat da tela Live é um espaço reservado: o chat de verdade vem do Restream.

1. No Restream, abra a área de **Chat** e copie o link de **Embed para OBS**.
2. No OBS, na cena Live, adicione uma fonte **Navegador** com esse link.
3. Tamanho `368 × 673`, posição `1526, 381`.
4. Coloque essa fonte **acima** do overlay na lista.

O visual e as opções do chat dependem do que o Restream oferece na conta.

---

## 7. Alertas

### Testar sem esperar a live

Abra o HTML no navegador (clique duplo) e use as teclas. No OBS, clique com o botão direito na fonte, escolha **Interagir** e use as mesmas teclas.

| Tecla | Efeito |
|---|---|
| `1` a `5` | Troca de cena (Intro, Live, Game, Final, BRB) |
| `Q` | Alerta de novo inscrito (e soma na meta) |
| `W` | Alerta de novo membro |
| `E` | Alerta de Super Chat |
| `R` | Alerta de raid |
| `C` | Liga/desliga chat falso (só para teste visual) |
| `M` | Soma 1 na meta |

### Alertas reais do YouTube (StreamElements)

1. Crie uma conta no **StreamElements** e conecte o canal do YouTube.
2. Copie o **JWT Token** (Account, Channels, Show secrets).
3. Cole em `seJWT:""` dentro do `CFG` e salve.
4. Use o botão de teste de alertas do StreamElements. Se o alerta aparecer no overlay, está funcionando.

**Atenção:**
- Cuide do token como uma senha. **Nunca suba o arquivo com o token preenchido para o GitHub.**
- O mapeamento dos eventos do StreamElements para o YouTube (inscrito, membro, Super Chat, raid) foi feito por suposição. Se algum alerta não aparecer, ajuste a função `fromSE` no código.
- Cada cena é uma página separada: um alerta real só aparece na cena que estiver ativa no momento.

---

## 8. Escolhendo a versão certa

- **PC mais fraco ou jogo pesado:** use **Imagens**. Tem menos partículas e animações.
- **Quer o visual mais animado e personalizável:** use **Só código**. É a mais flexível para mudar cores, textos e efeitos.
- **Quer o melhor dos dois:** use **Híbrida**. Tem a arte da Ahri com movimento.

Pode **misturar** versões: por exemplo, a Intro da Híbrida e o resto da Só código. Cada cena é uma fonte separada, então basta usar o arquivo que quiser em cada URL.

---

## 9. Desempenho

Os brilhos, as molduras animadas e as partículas gastam placa de vídeo. Se a transmissão engasgar:

1. Confirme as opções da seção 3 (desligar fonte quando não visível, FPS 30).
2. Em **Configurações, Avançado**, deixe a aceleração de hardware da Fonte Navegador ligada.
3. Use a versão **Imagens**.
4. Reduza o número de partículas: no código, procure `Array.from({length:70}` e troque `70` por um valor menor.

---

## 10. Solução de problemas

| Problema | Causa provável | Solução |
|---|---|---|
| Todas as cenas mostram a Intro | "Arquivo local" está marcado | Desmarque e use a URL com `?scene=` |
| Imagens não aparecem | Imagens fora da pasta do HTML, ou nome diferente | Deixe tudo na mesma pasta, com os nomes originais |
| Tela em branco | Caminho da URL errado | Confira barras `/`, `%20` nos espaços e o nome do arquivo |
| Fonte de letra diferente | Sem internet na primeira carga | As fontes vêm do Google Fonts; conecte e atualize o cache |
| Sem som nos alertas | Áudio não controlado pelo OBS | Marque **Controlar áudio via OBS** |
| Jogo ou câmera "cobertos" | Ordem das fontes errada | Overlay acima do jogo e da câmera (o chat fica acima do overlay) |
| Janela do jogo desalinhada | Posição ou tamanho diferentes | Use os valores da seção 4 |
| Contagem não reinicia | Opção de atualizar desmarcada | Marque **Atualizar navegador quando a cena ficar ativa** |

---

## 11. Personalização rápida

- **Cores:** no começo do CSS, as variáveis `--cy` (azul), `--pu` (roxo) e `--gold` (dourado).
- **Textos grandes** (como "Começando em breve"): nas versões Só código e Híbrida, os textos estão no HTML da cena e podem ser editados direto. Nas imagens `intro.jpg` e `final.jpg`, os textos fazem parte da arte.
- **Trocar as artes:** substitua `intro.jpg` e `final.jpg` por outras imagens **1920×1080** com os mesmos nomes.
- **Trocar os logos:** substitua `logo-retrox.png` e `logo-lol.png` por PNGs com fundo transparente.

---

## 12. Avisos

- Os recursos foram testados apenas em teoria durante a criação; faça o roteiro de teste da seção 7 antes de entrar ao vivo.
- As artes foram geradas por IA pelo dono do canal.
- *League of Legends* e seu logo são marcas da **Riot Games**. Este projeto é conteúdo de fã, sem vínculo oficial com a Riot Games.
