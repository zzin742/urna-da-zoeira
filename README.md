# Urna da Zoeira

> **É brincadeira.** Simulação de urna eletrônica feita pra zoar os amigos. Não é a urna oficial, não tem
> nada a ver com o TSE e não serve de "prova" de coisa nenhuma: a troca de candidato é programada de propósito
> no código (objeto `PEGADINHA`). Todas as telas carregam a faixa amarela **SIMULAÇÃO · URNA ELETRÔNICA**.

Réplica em HTML do modelo UE2020 com o som real da urna (medido de gravações) e uma pegadinha no voto de presidente.
Todas as telas carregam a faixa amarela **SIMULAÇÃO · URNA ELETRÔNICA**. Não remova: é o que deixa claro que isso é brincadeira, não a urna oficial.

Arquivo único: `index.html` (sem dependências, é só abrir no navegador). Fotos em `fotos/`, áudios extras opcionais em `sons/`.

## O que tem
- Visual do modelo UE2020 (corpo cinza-claro, moldura preta, teclado numérico preto com Braille em relevo, BRANCO / CORRIGE / CONFIRMA, selo "Justiça Eleitoral" e "UE2020" em baixo-relevo).
- Telas iguais às da votação: SEU VOTO PARA, cargo, caixas de número, Nome / Partido / Vice ou Suplentes, fotos à direita, rodapé "Aperte a tecla: VERDE para CONFIRMAR este voto / LARANJA para REINICIAR este voto".
- Eleição só de Presidente (2 dígitos), com dois candidatos: 13 LULA (PT) e 22 FLÁVIO BOLSONARO (PL). Qualquer outro número dá VOTO NULO. Para voltar a ter mais cargos, acrescente entradas em `TURNOS[1]` e listas em `C`.
- VOTO EM BRANCO, VOTO NULO (NÚMERO ERRADO / CANDIDATO INEXISTENTE), VOTO DE LEGENDA, tela FIM.
- Pausa técnica de 1 s: com o número completo, CONFIRMA só vale depois de 1 segundo (igual à urna real).
- Terminal do mesário (botão LIBERAR ELEITOR). Volume e dicas ficam escondidos em "Configurações", pra não entregar o truque.
- Teclado físico: 0–9, Enter = CONFIRMA, Backspace = CORRIGE, B = BRANCO.

## Som (medido de gravações da urna real, Web Audio)
| Evento   | Frequência        | Duração                                   |
|----------|-------------------|-------------------------------------------|
| Tecla    | 2300 Hz senoidal  | ~22 ms                                    |
| CORRIGE  | 2 bips de tecla   | 184 ms entre os inícios                   |
| CONFIRMA | trinado 2300 (20 ms) → 2200/2300 alternando a cada ~90 ms | 0,70 s |
| FIM      | mesmo trinado     | 1,35 s                                    |

Fonte das gravações: amostras `confirma.mp3`/`numeros.mp3`/`corrige.mp3` de simuladores públicos no GitHub
(doni7brandao/urna, dimascapelari/urna-eletronica-vue, JoaoRoccella/urna-eletronica), analisadas por FFT.
Fotos de referência do hardware: Wikimedia Commons (UE2020).

## Pegadinha (troca de candidato)
No começo do script tem o objeto `PEGADINHA`:

```js
const PEGADINHA = {
  pres: { '22': { mostra: '13', quando: 'confirma', espera: 2600, som: null } },
};
```

- A chave é o cargo (hoje só `pres`); dentro dela, o número que a pessoa digita.
- `mostra` é o número do candidato que vai aparecer (tem que existir na lista `C` daquele cargo).
- `quando: 'confirma'`: a tela mostra o candidato digitado normalmente; ao apertar CONFIRMA toca o som de voto
  confirmado e a tela passa a mostrar o `mostra` (com o número dele) por `espera` ms, depois a urna segue
  (próximo cargo ou FIM). Durante a troca as teclas são ignoradas.
- `quando: 'digitar'`: a troca aparece assim que o número completa. `trocaNumero: true` troca os dígitos também.
- `som`: áudio extra que toca junto com a troca (arquivos em `sons/`). Sem o arquivo, toca uma fanfarra de teste.
- CORRIGE para o som e limpa; CONFIRMA registra o candidato que apareceu.

## Candidatos reais
Presidente 13 LULA (PT) e 22 FLÁVIO BOLSONARO (PL), fotos em `fotos/lula.jpg` e `fotos/flavio.jpg`
(copiadas de Downloads e reduzidas para 600 px). O campo `vice` ficou vazio de propósito: preencha em `C.pres`
se quiser que a linha e a foto do vice apareçam (`fotoVice: 'fotos/x.jpg'`).
