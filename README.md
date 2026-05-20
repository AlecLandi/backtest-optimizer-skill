# 🔬 backtest-optimizer-skill

> O Optimizer nativo testa combinações. Este protocolo testa hipóteses.
> Método causal e iterativo para otimização manual de backtests em sistemas de trading algorítmico.

---

## O que é

Um protocolo estruturado em 5 etapas para otimizar bots de trading através de testes causais, não força bruta. Documenta uma forma de pensar a otimização — taxonomia de parâmetros em 4 grupos funcionais, ciclo de hipóteses "uma família por vez", planilha de controle, e critérios anti-overfitting.

O conteúdo central está em [`SKILL.md`](./SKILL.md). Esse README é a porta de entrada.

---

## O problema

Quem usa Optimizer nativo (cTrader, MT5, NinjaTrader) sabe: você define ranges de parâmetros, a plataforma testa milhares de combinações, e te entrega o "melhor resultado". Três problemas com isso:

1. **Overfitting silencioso** — a combinação "ótima" foi escolhida porque ajusta o ruído do período histórico, não porque é causalmente boa
2. **Você não aprende nada** — testa cego, escolhe cego, não sabe *por que* funcionou
3. **Não evolui** — a próxima rodada começa do zero, sem hipóteses acumuladas

Este protocolo inverte a lógica: você formula hipóteses causais ("se eu reduzir X, espero Y, porque Z"), testa uma de cada vez, registra, e constrói entendimento real do comportamento do bot.

---

## Pra quem serve

- ✅ Traders algorítmicos que fazem backtest manual e estão saturados de chutar parâmetros
- ✅ Devs de bots em cTrader/C# (origem do método)
- ✅ Quants iniciantes que precisam de framework de pensamento, não de receita pronta
- ✅ Usuários de Claude Code que querem skills aplicadas a trading

## Pra quem não serve

- ❌ Trader discricionário sem backtests sistemáticos
- ❌ Day trader puro buscando sinais em tempo real
- ❌ Quem procura "robô pronto que ganha dinheiro" — não tem código de bot aqui, só método

---

## Como o método funciona — 5 etapas

1. **Preparar base** — período fixo de backtest, estrutura de pastas, métricas de sucesso (PF > 1.3, MaxDD < 20%, etc.)
2. **Baseline e diagnóstico** — rodar bot com parâmetros atuais e diagnosticar assimetria BUY/SELL, regime de volatilidade, qualidade da gestão
3. **Taxonomia de parâmetros** — classificar tudo em 4 grupos: Entrada / Gestão / Filtros / Aprendizado adaptativo
4. **Ciclo de hipóteses** — uma família por vez, com hipótese formulada, teste, decisão (mantém / reverte / explora mais)
5. **Validação anti-overfitting** — rodar em período diferente; se PF cair mais de 20%, é overfitting

Detalhamento completo em [`SKILL.md`](./SKILL.md).

---

## Exemplo de hipótese bem formulada

> "O `StopAtrMult` de 1.8 pode estar largo demais em regime Normal.
> Reduzir para 1.5 deve diminuir o MaxDD sem perder muitos trades."

Estrutura: alteração concreta + efeito esperado + razão causal. Sem isso, o teste é chute.

---

## Como usar

Três caminhos, do mais leve ao mais integrado:

### 1. Ler e aprender
Abra [`SKILL.md`](./SKILL.md) e absorva o método. Mesmo sem aplicar diretamente, você sai com uma forma diferente de pensar otimização.

### 2. Copiar e adaptar
Use a planilha de controle (Etapa 4), a estrutura de pastas, e os benchmarks como template pro seu próprio bot — não precisa ser cTrader, não precisa ser o bot original.

### 3. Instalar como skill do Claude Code
Baixe o `SKILL.md`, coloque em `~/.claude/skills/backtest-optimizer/SKILL.md`, e o Claude passa a aplicar o protocolo nos seus backtests automaticamente.

---

## Limitações e contexto de origem

Honestidade: esse protocolo nasceu como skill privada de uso pessoal, escrita pra Claude usar comigo nos backtests do meu cBot (BanditThompson V3.x). Por isso, no `SKILL.md` você vai encontrar referências a "o Landi" — sou eu, é vestígio honesto de como o material surgiu.

O **método** é genérico (taxonomia, ciclo de hipóteses, validação cruzada). Os **exemplos** são específicos do meu contexto (cTrader, C#, indicadores HMM/BCPD/Thompson Sampling). Adaptar pro seu caso é direto: a estrutura de pensamento serve qualquer bot que tenha parâmetros e gere histórico de trades.

---

## Roadmap

**v1 (atual)** — método publicado como referência. Evolui conforme uso real e feedback recebido via Issues.

---

## Feedback

Encontrou um furo no método? Aplicou e funcionou (ou não)? Tem sugestão de etapa que faltou? Abra uma **[Issue](../../issues)**. Leio todas.

---

## Sobre o autor

Landi. 15 anos em banking, hoje em transição pra dev/SaaS com foco em IA aplicada a trading. Desenvolvo C#Bots e exploro como IA pode automatizar análises de mercado.

🔗 [LinkedIn](https://linkedin.com/in/alecssandro-roger-landi-35455624)

---

## Licença

[MIT](./LICENSE) — uso livre com atribuição.

## ⚠️ Disclaimer

Material educacional. Trading envolve risco real de perda financeira. Backtest não garante performance futura. Não é recomendação de investimento. Resultados em período passado podem não se repetir em condições reais de mercado.
