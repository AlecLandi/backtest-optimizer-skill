---
name: backtest-optimizer
description: >
  Protocolo completo de otimização iterativa para o cBot BanditThompson V5.0–V7.0
  no cTrader Desktop. Use SEMPRE que Landi mencionar backtests, relatórios de trades,
  logs de parâmetros, ajuste de parâmetros do bot, ou quiser melhorar performance do
  Bandit Thompson. Também use quando mencionar Profit Factor, Win Rate, Drawdown, HMM,
  BCPD, Thompson Sampling, Walk-Forward, gates condicionais, regime direcional, ou
  qualquer métrica de resultado do cTrader. Contém o protocolo completo de otimização
  com 5 fases estruturadas, critérios de aprovação validados em produção, e todos os
  aprendizados acumulados do projeto V3.2→V7.0.
---

# 🔬 Backtest Optimizer — BanditThompson V5.0→V7.0

> Protocolo de otimização estruturado em 5 fases, validado em produção.
> Cada fase tem critérios de saída numéricos. Nunca pular fases.

---

## ⚡ INÍCIO RÁPIDO

Se o Landi trouxer um relatório HTML + log TXT, seguir esta ordem:
1. Extrair métricas do HTML (ROI, PF geral, PF BUY, PF SELL, trades, DD)
2. Extrair do log: HMM strictBars%, HMM toggles/dia, WF resets
3. Identificar o perfil: parâmetros dentro das faixas seguras? Stop SELL >= 0.9?
4. Comparar com as referências da seção de benchmarks
5. Classificar como válido ou descartável (ver seção "Filtro de amostras")

---

## 📊 BENCHMARKS DE REFERÊNCIA (XAUUSD Hm15)

| Versão | Período | Trades | PF | PF SELL | Net SELL | DD | Status |
|---|---|---|---|---|---|---|---|
| V3.2C Opt7 | 2025 | 475 | 1,23 | 1,08 | $695 | 5,8% | Histórico |
| V5.0 Opt7 RF30 | 2025 | 287 | 1,83 | 1,83 | $660 | 24,2% | Superado |
| V6.0 A10 (Fase2) | 2025 | 291 | 2,02 | 2,56 | $1.459 | 27,0% | Base Fase3 |
| V6.0 A12 (Fase2) | 2025 | 291 | 2,10 | 2,70 | $1.506 | 27,4% | Melhor Fase2 |
| V6.0 gates on | 2025 | 391 | 1,98 | 2,24 | $2.390 | 30,3% | Live aprovado |

### Critérios mínimos de aprovação (XAUUSD Hm15)
| Critério | Mínimo | Excelente |
|---|---|---|
| PF geral (otimização) | >= 1,75 | >= 2,00 |
| PF SELL (otimização) | >= 1,70 | >= 2,20 |
| PF geral (validação 2024) | >= 1,25 | >= 1,40 |
| PF SELL (validação 2024) | >= 0,72* | >= 0,85 |
| Trades totais | >= 280 | >= 380 |
| Max DD | <= 30% | <= 25% |
| Max consec. perdas | <= 13 | <= 10 |

*Teto real de 2024 (bull run +32%): PF SELL >= 1,20 é irrealista. 0,72–0,85 é o range atingível.

---

## 🚨 FILTRO DE AMOSTRAS — Descartar antes de analisar

Uma amostra é **descartável** se qualquer um desses sinais aparecer:

| Sinal | Threshold de descarte | Por quê |
|---|---|---|
| Trades SELL | < 5 | Sem significância estatística |
| Stop ATR SELL | < 0,5 | PF SELL artificial (trades fecham no ruído) |
| Stop ATR BUY | < 0,5 | Idem |
| TP R BUY ou SELL | > 3,5 | TP inalcançável em Hm15 |
| Trail ATR | > 3,0 | Trailing nunca aciona |
| ATR Period | != 12 (se livre) | Mudança estrutural não autorizada |
| SMC Swing Span | != 8 (se livre) | Idem |
| HMM Decay | < 0,01 | HMM congelado — regime inútil |
| HMM StrictON | < 0,05 | Filtro de HighVol praticamente desligado |
| HMM Strict% | < 5% | Bot rodou sem proteção de regime |
| BreakConfirm SELL ATR% | > 0,5 | Bloqueia todos os SELLs em Hm15 |
| BCPD Hazard | > 0,05 | BCPD dispara a cada poucos trades |
| WF Window | < 30 | WF reseta sem dados suficientes |
| Cooldown BUY/SELL | > 5 barras | Bot paralisa por horas |

---

## 🔒 PARÂMETROS FIXOS — Nunca otimizar

Convergidos em 12 amostras da Fase 2. Fixar como valores únicos no otimizador:

| Parâmetro | Valor | Evidência |
|---|---|---|
| Pin Bar Wick Ratio | 0,55 | 12/12 amostras |
| Retest Zone ATR BUY | 0,95 | 12/12 amostras |
| Candle Confirm BUY | true | 12/12 amostras |
| Time Decay Min R BUY | 1,95 | 12/12 amostras |
| Time Decay Min R SELL | 0,05 | 12/12 amostras |
| Partial SELL | @10R / 59% | 12/12 amostras |
| Bandit R Cap | 0,5 | 12/12 amostras |
| Bandit Cost R | 0,43 | 12/12 amostras |
| MFE Quality Mult | 0,70 | 12/12 amostras |
| SL Extra ATR Calm/Normal/HighVol | 1,8 / 1,0 / 1,05 | 12/12 amostras |
| TP Mult Calm/Normal/HighVol | 1,0 / 1,9 / 2,2 | 12/12 amostras |
| WF Window | 73 | 12/12 amostras |
| WF Min Win Rate | 0,60 | 12/12 amostras |
| WF Min Profit Factor | 0,50 | 12/12 amostras |
| WF Reset Factor | 0,30 | 12/12 amostras |
| HMM Decay | 0,03 | Fixado na Fase 1 |
| HMM StrictON / StrictOFF | 0,60 / 0,35 | Fixado na Fase 1 |
| SMC Swing Span | 8 | Fixado na Fase 1 |
| ATR Period | 12 | Fixado na Fase 1 |
| BreakConfirm ATR% SELL | 0,3 | Fixado na Fase 1 |
| Bandit Arms | 2 | 12/12 amostras |
| Bandit Reward Mode | RMultiple | 12/12 amostras |

---

## 📐 FAIXAS SEGURAS — Limites máximos no otimizador

| Parâmetro | Mínimo | Máximo | Observação |
|---|---|---|---|
| Stop ATR Mult BUY | 0,9 | 2,5 | Abaixo de 0,9 é anômalo |
| Stop ATR Mult SELL | 0,9 | 2,5 | 1,9 = padrão forte (4/12 Top amostras) |
| TP R BUY | 1,9 | 3,5 | |
| TP R SELL | 2,7 | 3,5 | >= 2,7 em 11/12 amostras |
| Trail ATR Mult BUY/SELL | 0,1 | 3,0 | |
| Cooldown Bars BUY/SELL | 0 | 5 | |
| BreakConfirm ATR% BUY | 0 | 0,5 | |
| OB Lookback SELL | 5 | 20 | Tende a <= 7 (8/12 amostras) |
| OB Lookback BUY | 8 | 20 | |
| Retest Timeout BUY/SELL | 10 | 34 | Dois perfis: ~15 vs ~30 |
| Retest Zone ATR SELL | 0,14 | 0,70 | 0,31 mais frequente |
| BCPD Hazard | 0,0005 | 0,01 | |
| WF Min Win Rate | 0,50 | 0,65 | |

---

## 🔄 AS 5 FASES DO PROJETO

### Fase 1 — Baseline e otimização do período principal
**Objetivo:** encontrar a melhor configuração no período de otimização
**Período:** jan/2025 → abr/2026 (ou equivalente para outros papeis)
**Mínimo de amostras:** 5 para análise, 12 para convergência
**Critério de saída:** PF >= 1,75 e PF SELL >= 1,70

**Como analisar as amostras:**
- Agrupar por Stop ATR SELL (padrão convergido = 1,9)
- Identificar duas sub-populações: perfil ágil (Timeout SELL ~15) vs paciente (~30)
- O perfil com Q2 positivo tende a ser o ágil
- Amostras com Stop SELL < 0,5 ou > 2,5: descartar (ver Filtro)

### Fase 2 — Validação cruzada (out-of-sample)
**Objetivo:** confirmar robustez em período não usado na otimização
**Período:** fev/2024 → jan/2025 (bull run +32%)
**Critério de aprovação:**
- PF geral >= 1,25
- PF SELL >= 0,72 (teto real do bull run — não exigir mais que isso)
- ROI positivo
- DD <= 8%

**⚠️ Armadilha de 2024:** mais trades SELL em 2024 = mais perdas em bull run.
Gates que liberam SELL em HighVol funcionam em 2025 mas pioram em 2024.
O OB gate (OBLookback SELL pequeno) neutraliza o OB Required gate — desligar OB gate
quando OBLookback <= 7 não muda nada porque o OB já é satisfeito naturalmente.

### Fase 3 — Gate condicional HMM (V6.0)
**Mudança arquitetural:** filtros condicionais por regime
- MTF Only On Calm/Normal: desliga MTF em HighVol
- CandleConfirm SELL Only On Calm: desliga confirmação em Normal/HighVol
- OB Required Only On Calm: desliga OB gate em Normal/HighVol
**Resultado confirmado:** +35% volume de trades, +64% Net SELL, mas DD pode cruzar 30%

### Fase 4 — HMM Direcional (V7.0)
**Mudança arquitetural:** adiciona TrendBias ao gate de SELL
- TrendBias = Bearish/Neutral/Bullish baseado em EMA H1
- SellGateState = FullFilter/Partial/Open baseado em Regime × TrendBias
- HighVol + Bullish = SELL bloqueado (resolve o problema de 2024)
- HighVol + Bearish = SELL completamente livre

### Fase 5 — Live com escalonamento
- 60 dias com 0,01 lot
- Stop manual: DD acumulada > 8% → pausar
- Escalar para 0,02 lot após 60 dias positivos
- Monitorar PF BUY e SELL vs backtest (tolerância ±0,20)

---

## 🧠 PADRÕES APRENDIDOS EM PRODUÇÃO

### Sobre o SELL em bull run
O SELL em XAUUSD em mercado de alta moderada (+20–35% ao ano) tem teto de PF ~0,83–1,08.
Qualquer configuração que abra mais trades SELL em bull run aumenta as perdas.
O OB filter em HighVol tinha uma função implícita: filtrar SELL em rally (OBs bearish raramente aparecem em HighVol de alta).

### Sobre o Stop ATR SELL
Stop ATR SELL = 1,9 convergiu nas 4 melhores amostras da Fase 2 (A3, A6, A10, A12).
Stop SELL < 0,5 infla o PF artificialmente — trades fecham no ruído antes de ir contra.
Sinal de alerta: PF SELL alto com Stop SELL < 0,5 → descartar.

### Sobre os dois perfis de Timeout SELL
Perfil ágil (Timeout ~15 barras): melhor Q2, captura correções rápidas
Perfil paciente (Timeout ~30 barras): melhor Q4/Q1, aguarda setups mais qualificados
Ambos funcionam — a escolha depende do período de mercado dominante.

### Sobre o HMM
HMM Strict% saudável: 40–50% do tempo em modo strict
HMM toggles/dia saudáveis: 5–8 por dia
Se Strict% < 10%: HMM Decay muito baixo ou StrictON muito alto — HMM inativo
Se toggles/dia > 15: HMM instável, mercado muito ruidoso

### Sobre o Walk-Forward
WF com Min Win Rate = 0,60 e RF = 0,30 é o padrão validado
WF disparando > 4x por ano pode indicar que o bot está em regime adverso prolongado
WF Reset com pf > 1,5 na hora do reset = reset desnecessário (critério muito agressivo)

### Sobre o DD
DD do V6.0 em 2025 = 27–30%. Esse nível é aceitável dado ROI de 33–44%.
DD em 2024 (período adverso) = 4–5%. Isso mostra que o bot não quebra em mercado ruim.
DD causado por sequência SELL > DD causado por trade individual BUY.
Ajustar Stop BUY quando o DD vem do SELL é ineficaz.

---

## 📋 PROTOCOLO DE ANÁLISE DE RELATÓRIO

Quando o Landi traz HTML + TXT:

**1. Verificar se é válido (30 segundos):**
- Trades SELL > 5?
- Stop ATR SELL >= 0,9?
- HMM Strict% entre 30–55%?
- Se não → descartar imediatamente, explicar o motivo

**2. Extrair métricas completas:**
- ROI, PF all/BUY/SELL, Trades all/BUY/SELL
- Net BUY e Net SELL separados
- Avg/trade, Max consec. perdas (all/BUY/SELL)
- Max DD, período de mínimo
- Trimestres: Q1/Q2/Q3/Q4 e Q1/2026

**3. Comparar com referências:**
- Tabela com Opt7 RF30, A10, A12 e resultado atual
- Destacar novos recordes com ★
- Alertar sobre regressões

**4. Identificar convergências:**
- Manter tabela acumulada de parâmetros convergidos
- Depois de 5+ amostras: identificar padrões por sub-grupo

**5. Propor próximo passo:**
- Se PF SELL com Stop < 0,5: avisar e pedir próxima amostra
- Se bom resultado: confirmar com validação cruzada
- Se aprovado em ambos: avançar para próxima fase

---

## 🔗 REFERÊNCIAS CRUZADAS
- Para modificar o código do bot: skill `ctrader-bots`
- Para SDD das próximas versões: ver documentos V6.0 SDD e V7.0 SDD
- Para análise de regime HMM: ver seção "Padrões aprendidos"
