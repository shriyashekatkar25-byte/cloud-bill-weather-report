# Cloud Bill Weather Report

**Should budgets warn admins *before* a month overspends, and how sure should the forecast be before it emails anyone?**

- **Live demo:** https://shriyashekatkar25-byte.github.io/cloud-bill-weather-report/
- **Gallery:** https://shriyashekatkar25-byte.github.io/apm-lab/ (APM Lab, a new product-decision prototype 3× a day)

By Shriya Shekatkar · UCLA · Stats 102C Monte Carlo Methods

## The launch it mirrors

On **October 16, 2026**, Databricks budgets gained access control lists, so admins can give users, groups or service principals the **Budget Manager** or **Budget Viewer** role ([release notes, October 2026](https://docs.databricks.com/aws/en/release-notes/product/2026/october)). Product facts I checked this run:

- Budgets track monthly spend in USD at list price and can have up to four thresholds, each with email recipients. Alerts fire when spend crosses a threshold and can lag usage by up to 24 hours. The budgets page doesn't describe any forecast ([Budgets docs](https://docs.databricks.com/aws/en/admin/account-settings/budgets)).
- Serverless spend is identified in `system.billing.usage` via `product_features.is_serverless` and `billing_origin_product`, with `usage_quantity` in DBUs ([Monitor the cost of serverless compute](https://docs.databricks.com/aws/en/admin/system-tables/serverless-billing)).
- `system.billing.usage` joins to `system.billing.list_prices` to estimate spend ([Top 5 System Table Queries for Understanding Your Databricks Costs, Sept 22, 2026](https://www.databricks.com/blog/top-5-system-table-queries-understanding-your-databricks-costs)).

No new Databricks or data-platform launch had come out in the previous 48 hours, so this project is backlog idea #2 (cloud-bill weather report), tied to this month's budgets launch.

## The PM decision

With more people now watching each budget, should we add an opt-in **"likely to exceed" forecast alert**? If so, how high should the odds of overspend be before it fires?

- **Customer problem:** a threshold email only arrives after the money is spent (and up to a day late). Admins need time to pause a schedule or resize a warehouse.
- **Success metric:** early-warning rate, the share of over-budget months warned **≥ 5 days** before spend crosses the line (target ≥ 60%).
- **Guardrails:** false alarms ≤ 15% of on-budget months · recall ≥ 70% · median lead ≥ 5 days · ≤ 1 forecast email per budget per month.
- **Stakeholders:** account/workspace admins, the new Budget Viewers, FinOps/finance, billing platform engineering, support, account teams.

## What the prototype shows

| APM skill | Where it shows up |
|---|---|
| Customer problem framing | The "surprise bill" story and a success metric that measures warning *time*, not accuracy alone |
| Prototyping | A live simulator: the day of the month, budget, odds trigger, workload burstiness and runaway-job risk are all adjustable |
| SQL / Python | Illustrative queries over the billing system tables and a NumPy forecaster |
| Data-driven decisions | A backtest over 160 simulated months comparing 25% to 95% odds triggers against every guardrail; the memo picks the best trigger that passes |
| Clear writing | A director memo that rewrites itself, plus a one-sentence VP summary |

With the default settings, a **70% odds trigger** warns about 88% of overspending months 5+ days early (median lead about 13 days), with false alarms on about 7% of on-budget months. A 25% trigger warns earlier but false-alarms on nearly half of on-budget months. All numbers are simulated.

## Methods

| Course idea (Stats 102C) | Used for |
|---|---|
| Ch3 inverse CDF (discrete) | Daily run counts per workload from weekday/weekend probability tables |
| Ch3 inverse CDF (exponential, geometric) | Single-stage run cost; runaway-job duration |
| Ch5 convolution | Run cost = sum of k exponential stages (Erlang); month-end bill = sum over runs, days and workloads |
| Ch5 Box-Muller | Backtest shortcut: Gamma(Nk, θ) total drawn as a CLT normal; lognormal uncertainty in the run-rate |
| Ch1 Monte Carlo | 2,000 futures per forecast, seeded RNG (mulberry32), quantiles and overspend odds |

## Design baseline

Four pins from Shriya's Pinterest home feed, each redrawn in CSS/SVG (no pin images are used):

- **Hero:** [pink lotus bouquet by pipipiiiiiiii](https://www.pinterest.com/pin/955396508455264102/). Took the hot-pink lotus petals in a magenta box against a dark slatted backdrop, redrawn as an SVG bouquet on a striped charcoal background.
- **Cards & buttons:** ["Art is the Culture" by Pa' The Creator](https://www.pinterest.com/pin/1147784655065165813/). Took the bottle-green handwritten script on off-white, used for card tags with scribble underlines.
- **Simulation room:** ["111 A new cycle is quietly beginning", saved by Ricky David](https://www.pinterest.com/pin/569635052893126877/). Took the mint field, deep-teal heavy condensed type and small @handle caption.
- **KPI badges:** [candy gem makeup, @fairymusee via DΣΣ](https://www.pinterest.com/pin/1058416349944497847/). Took the neon pink, lime, cyan and tangerine glossy sticker dots, rebuilt as glossy CSS gem badges.

---

Unofficial concept prototype by Shriya Shekatkar. All data is simulated. Not affiliated with any company.
