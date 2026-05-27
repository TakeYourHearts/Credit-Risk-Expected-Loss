# CLAUDE.md — credit-risk-expected-loss

> Arquivo de contexto e contrato de execução do projeto.
> Lido por LLMs e por mim mesmo a cada sessão de trabalho.
> Nunca pular etapas. Cada fase tem critério de conclusão explícito.

---

## 1. Contexto

**Autor:** João Lucas Ferreira de Oliveira
**Formação:** Ciências Contábeis + Data Science
**Objetivo de carreira:** Cientista de Dados / Modelagem de Risco de Crédito / Risco Quantitativo
**Objetivo do projeto:** Construir um pipeline completo de perda esperada (Expected Loss) de crédito que sirva como peça central de portfólio para vagas de risco — Sicredi, HUPDATA, XP, Nubank Credit, Total Pass.

---

## 2. Escopo

Modelar **Expected Loss (EL) = PD × LGD × EAD** sobre o dataset Home Credit Default Risk (Kaggle), comparando regressão logística (padrão de mercado) com XGBoost (benchmark), e gerando um relatório executivo final.

### Dentro do escopo
- EDA completa orientada a risco
- WoE / IV / binning monotônico
- Scorecard de PD com regressão logística (pontuação 300–850)
- Modelo de PD com XGBoost + SHAP
- LGD estimado por segmento, com premissas baseadas em literatura (paper BCB WPS193)
- EAD com fórmula simplificada (saldo × fator de utilização)
- Cálculo de EL por cliente e análise de portfólio
- Relatório PDF executivo + README profissional + repositório público

### Fora do escopo (decisão explícita)
- Monitoramento em produção (PSI, CSI) — dados estáticos, fora do escopo, mencionar como "próximos passos"
- Deploy em API ou MLOps — não é foco de Jr/Pleno em risco
- Estimação econométrica avançada de LGD (Beta regression, fractional logit) — fora do nível Jr

---

## 3. Stack técnico

- **Linguagem:** Python 3.11+
- **Ambiente:** VS Code + Jupyter
- **Bibliotecas:** pandas, numpy, scikit-learn, xgboost, statsmodels, scorecardpy ou optbinning, shap, matplotlib, seaborn
- **Versionamento:** Git + GitHub (repositório público)
- **Entrega final:** Notebooks numerados por fase, `src/utils.py` com funções extraídas no final, README, relatório PDF

---

## 4. Estrutura de pastas

```
credit-risk-expected-loss/
├── data/
│   ├── raw/              # .gitignore — dataset original do Kaggle, nunca modificado
│   └── processed/        # todos os dados gerados: limpos, WoE, splits
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_woe_iv.ipynb
│   ├── 03_pd_logistic.ipynb
│   ├── 04_pd_xgboost.ipynb
│   ├── 05_lgd_ead.ipynb
│   └── 06_expected_loss.ipynb
├── src/
│   └── utils.py          # funções extraídas dos notebooks — preenchido na Fase 7
├── reports/
│   ├── figures/          # todos os plots salvos
│   └── relatorio_final.pdf
├── references/           # papers e benchmarks (ex: BCB WPS193)
├── README.md
├── CLAUDE.md
├── requirements.txt
└── .gitignore
```

**Convenção de dados:**
- `data/raw/` — nunca modificar. É o dado original do Kaggle.
- `data/processed/` — tudo que é gerado pelo código: dados limpos, encodados com WoE, splits de treino/teste. Nomear de forma descritiva: `train_woe.csv`, `test_clean.csv`, `df_scores.csv`.

**Convenção de notebooks:**
- Cada notebook corresponde a uma fase.
- Todo notebook começa com uma célula markdown explicando o objetivo da fase.
- Decisões importantes são explicadas em prosa (markdown), não apenas em código.
- Plots são salvos em `reports/figures/` além de exibidos inline.

---

## 5. Fases e critérios de conclusão

Cada fase só é considerada concluída quando TODOS os critérios estão atendidos. Sem exceção.

### Fase 0 — Setup
- [x] Repositório criado no GitHub (público)
- [ ] Estrutura de pastas criada conforme seção 4
- [ ] `requirements.txt` com versões fixadas
- [ ] `.gitignore` com `data/raw/`, `.venv/`, `__pycache__/`
- [ ] README inicial com descrição do projeto
- [ ] Primeiro commit + push

### Fase 1 — EDA (notebook 01)
- [ ] Dataset baixado e em `data/raw/`
- [ ] Distribuição da variável `TARGET` (default rate) calculada e plotada
- [ ] Tabela de missing por variável (% e absoluto)
- [ ] Identificação de outliers nas variáveis numéricas principais
- [ ] Dicionário de dados em markdown no notebook
- [ ] Pelo menos 5 hipóteses de negócio levantadas e testadas visualmente

### Fase 2 — WoE / IV (notebook 02)
- [ ] Binning monotônico aplicado a todas as variáveis numéricas relevantes
- [ ] WoE calculado por bin
- [ ] IV calculado por variável
- [ ] Variáveis classificadas: IV < 0.02 descartadas, 0.02–0.1 fracas, 0.1–0.3 médias, > 0.3 fortes
- [ ] Pelo menos 15 variáveis selecionadas para modelagem
- [ ] WoE plots salvos em `reports/figures/`

### Fase 3 — PD logística + scorecard (notebook 03)
- [ ] Encoding WoE aplicado às variáveis selecionadas
- [ ] Split treino/teste (estratificado) salvo em `data/processed/`
- [ ] Regressão logística treinada (statsmodels para ter p-valores)
- [ ] Variáveis com p-valor > 0.05 reavaliadas
- [ ] Métricas em treino e teste: AUC, KS, Gini
- [ ] Curva ROC plotada e salva em `reports/figures/`
- [ ] Score 300–850 calculado e distribuição plotada
- [ ] Análise de cutoff (taxa de aprovação vs default rate)
- [ ] Coeficientes interpretados em prosa no notebook

### Fase 4 — PD XGBoost (notebook 04)
- [ ] XGBoost treinado com as mesmas features (não-WoE permitido aqui)
- [ ] Hyperparameter tuning básico (GridSearchCV ou Optuna)
- [ ] Métricas: AUC, KS, Gini (mesmas da Fase 3)
- [ ] SHAP summary plot + dependence plot para top 5 features — salvos em `reports/figures/`
- [ ] Comparação direta com regressão logística em tabela markdown no notebook

### Fase 5 — LGD e EAD (notebook 05)
- [ ] LGD: premissas documentadas com citação do paper BCB WPS193
- [ ] LGD segmentado por tipo de crédito presente no dataset
- [ ] EAD: fórmula definida e justificada (saldo × fator de utilização)
- [ ] Distribuição de LGD e EAD plotada e salva em `reports/figures/`
- [ ] Limitações da abordagem documentadas em prosa
- [ ] Resultado salvo em `data/processed/df_lgd_ead.csv`

### Fase 6 — Expected Loss (notebook 06)
- [ ] EL = PD × LGD × EAD calculado por cliente
- [ ] Distribuição de EL plotada (histograma + boxplot)
- [ ] Top 10 maiores exposições identificadas
- [ ] EL total do portfólio calculado
- [ ] Análise por segmento (faixa de score, tipo de crédito)
- [ ] Comparação de EL usando PD da logística vs PD do XGBoost

### Fase 7 — Entrega
- [ ] README profissional com: contexto, metodologia, resultados, métricas-chave em destaque
- [ ] Relatório PDF executivo (5–10 páginas) em `reports/relatorio_final.pdf`
- [ ] `src/utils.py` com funções principais extraídas dos notebooks (WoE, scorecard, métricas)
- [ ] Repositório limpo, sem dados pesados, com licença MIT
- [ ] Link do repositório adicionado ao LinkedIn e currículo

---

## 6. Princípios de execução

1. **Nenhuma fase começa antes da anterior estar 100% concluída.** Marcar checkboxes acima.
2. **Tudo em prosa, não só código.** Cada notebook tem markdown explicando *por que* aquela decisão foi tomada — não só *o que* foi feito. Recrutadores leem prosa.
3. **Métricas sempre em treino E teste.** Overfitting é o erro mais comum em portfólio Jr.
4. **Premissas sempre documentadas com fonte.** Especialmente LGD e EAD.
5. **Vocabulário de risco, não de ML genérico.** Falar "inadimplência" em vez de "label positiva", "concessão" em vez de "predição".
6. **Commits frequentes e descritivos.** `git commit -m "feat: WoE binning para variáveis demográficas"` é melhor que `"update"`.
7. **Quando travar, voltar a este arquivo antes de improvisar.**

---

## 7. Referências bibliográficas

- Banco Central do Brasil. *Loss Given Default: um estudo sobre perdas em operações de crédito.* Working Paper Series 193, 2007. https://www.bcb.gov.br/pec/wps/port/wps193.pdf
- Kaggle. *Home Credit Default Risk.* https://www.kaggle.com/c/home-credit-default-risk
- Siddiqi, Naeem. *Credit Risk Scorecards: Developing and Implementing Intelligent Credit Scoring.* Wiley, 2017.
- BCBS (Basel Committee). *International Convergence of Capital Measurement and Capital Standards.* Basileia II, 2006.

---

## 8. Cronograma

| Semana | Período | Fases | Horas estimadas |
|--------|---------|-------|-----------------|
| 1 | 26 mai – 01 jun | Fase 0 + Fase 1 (Setup + EDA) | ~12h |
| 2 | 02 jun – 08 jun | Fase 2 + Fase 3 (WoE/IV + PD logístico) | ~15h |
| 3 | 09 jun – 15 jun | Fase 4 (XGBoost + SHAP + comparação) | ~12h |
| 4 | 16 jun – 22 jun | Fase 5 + Fase 6 (LGD, EAD, EL) | ~12h |
| 5 | 23 jun – 30 jun | Fase 7 (README, PDF, entrega final) | ~10h |

**Deadline:** 30 de junho de 2026
**Total estimado:** 55–65 horas

---

## 9. Status atual

**Fase atual:** Fase 0 — Setup
**Última atualização:** [preencher na primeira sessão]
**Próximo passo:** Criar repositório no GitHub e estrutura de pastas

### Progresso geral
- [ ] Fase 0 — Setup
- [ ] Fase 1 — EDA
- [ ] Fase 2 — WoE / IV
- [ ] Fase 3 — PD logístico
- [ ] Fase 4 — XGBoost
- [ ] Fase 5 — LGD e EAD
- [ ] Fase 6 — Expected Loss
- [ ] Fase 7 — Entrega
