# 💰 Finanças do Casal — Open Banking → Postgres → n8n → Dashboard

Ecossistema de automação financeira pessoal do casal (Matheus & Ariane). A geração atual abandona a planilha e a digitação manual: os dados vêm direto dos bancos via **Open Finance (Pluggy)**, são classificados e gravados no **Postgres**, e viram **relatórios no Telegram** + um **dashboard web**.

> 📊 O dashboard (app Next.js) fica em repositório separado: **[matheus-ferrazzi/finance-dash](https://github.com/matheus-ferrazzi/finance-dash)**

## 🏗️ Arquitetura

```
Pluggy (Open Finance)  ──►  n8n (ingestão + classificação)  ──►  Postgres
                                                                    │
                                    ┌───────────────────────────────┼───────────────┐
                                    ▼                               ▼               ▼
                              Bots Telegram                    Dashboard        (backups)
```

Cada lançamento recebe uma **`classe`** (`despesa` · `receita` · `aporte` · `transferencia_interna` · `pagamento_fatura` · `estorno`), o que garante números reais (transferências entre contas e pagamentos de fatura ficam fora dos totais). A categoria da Pluggy passa por uma **camada de correção** (ex.: PlayStation que a Pluggy marcava como "aposta", pagamento de fatura via boleto, RDB da caixinha, etc.).

## 🧩 Workflows

### Ingestão (Pluggy → Postgres)
| Arquivo | O que faz |
|---------|-----------|
| `Finance 2.0.json` | Ingestão principal a cada 3h: autentica na Pluggy, busca transações (paginado), classifica (`classe` + correção de categoria) e faz upsert em `financas_lancamentos`. |
| `Pluggy - Faturas Cartao de Credito.json` | Atualiza `financas_faturas` (fatura atual, limite, vencimento) por cartão. |
| `Pluggy - Investimentos.json` | Atualiza `financas_investimentos` (posição da carteira, exclui resgatados). |
| `Pluggy - Saldos das Contas.json` | A cada 3h (:30): lê o saldo real de cada conta corrente e faz upsert em `financas_saldos`. É o ponto de partida da projeção do dashboard. |

### Bots Telegram (relatórios)
| Arquivo | O que faz |
|---------|-----------|
| `Bot 01 - Assistente Financeiro Interativo.json` | Assistente interativo (Telegram trigger) — *atualmente desativado*. |
| `Bot 02 - Relatorio Semanal de Financas.json` | Relatório semanal do casal, com resumo por IA (Groq), tom sóbrio. |
| `Bot 03 - Relatorio Diario 3x por Semana.json` | Check-in diário curto (gastos do dia vs. semana/mês). |
| `Bot 04 - Atualizacao de Investimentos.json` | Panorama de investimentos do casal. |
| `Bot 05 - Saude das Conexoes.json` | Observabilidade: alerta se alguma coleta (lançamentos/faturas/investimentos) atrasar. |
| `Bot 06 - Orcamento por Categoria.json` | Lê a tabela `financas_orcamento` e alerta ao atingir 80% do teto de cada categoria. |
| `Bot 07 - Relatorio do Casal.json` | Relatório consolidado do casal (despesa/receita por mês, top categorias, faturas, investido). |

### Bots Telegram (alertas)
| Arquivo | O que faz |
|---------|-----------|
| `Bot 08 - Alerta de Vencimento de Fatura.json` | Diário (9h): avisa quando uma fatura entra na janela de 0–3 dias do vencimento (alerta uma vez por ciclo). |
| `Bot 09 - Alerta de Gasto Grande.json` | De hora em hora: alerta compras acima de R$ 500 assim que são ingeridas (dedup via `staticData`, sem repetir). |
| `Bot 10 - Fechamento de Mes.json` | Dia 1º (9h): resumo do mês que fechou — receita, despesa (vs mês anterior), saldo, top categorias e maior gasto. |

## 🗄️ Tabelas (Postgres)

`financas_lancamentos` · `financas_orcamento` · `financas_faturas` · `financas_investimentos` · `financas_patrimonio` · `financas_saldos` · `financas_compromissos`

## 🔐 Segurança

- Credenciais (Pluggy, Postgres, Telegram, Groq) ficam **fora** dos workflows — referenciadas por env/credenciais do n8n, nunca hardcoded.
- Os exports neste repositório foram **sanitizados** (chat IDs trocados por placeholder).
- Dashboard exposto via Cloudflare Tunnel + **Cloudflare Access** (login por e-mail); banco lido por usuário **somente-leitura**.

## 🧠 Stack

n8n (self-hosted) · Pluggy (Open Finance) · PostgreSQL · Groq (Llama) para os relatórios · Telegram · Docker · Cloudflare Tunnel/Access

---

> 📁 A geração anterior (baseada em Google Sheets + digitação manual) está preservada em [`_legacy-planilha/`](./_legacy-planilha) para referência histórica.

*Mantido por [Matheus Ferrazzi](https://github.com/matheus-ferrazzi).*
