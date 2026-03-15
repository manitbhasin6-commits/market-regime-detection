# Market Regime Detection Engine

A browser-based quantitative model that classifies simulated market conditions into **bull**, **bear**, and **high-volatility** regimes using a Hidden Markov Model on Geometric Brownian Motion price paths.

**[→ Live Demo](https://manitbhasin6-commits.github.io/market-regime-detection)**

---

## What it does

Stock markets don't move in one continuous mode. They cycle through distinct behavioural states — trending up, trending down, or oscillating wildly. These states are called **regimes**.

The problem: you can't observe a regime directly. You can only observe prices. This model infers the hidden regime from price behaviour at each point in time, then colours the chart accordingly.

- 🟢 **Bull** — positive drift, low volatility, regime persists ~95% of days
- 🔴 **Bear** — negative drift, moderate volatility, regime persists ~93% of days  
- 🟠 **Volatile** — near-zero drift, high volatility (3× bull), regime persists ~90% of days

---

## How it works

### 1. Price simulation — Geometric Brownian Motion

Each daily price move is generated using GBM:

```
dS = μS·dt + σS·dW
```

| Parameter | Meaning |
|-----------|---------|
| `μ` (mu) | Daily drift — direction of travel. Bull: +0.08%/day, Bear: −0.06%/day |
| `σ` (sigma) | Volatility — size of random daily swings |
| `dW` | Random shock drawn from a normal distribution |

### 2. Regime transitions — Hidden Markov Model

A Markov Model is a system where tomorrow's state depends only on today's state. The transition matrix defines the probability of switching regimes on any given day:

```
          → Bull   → Bear   → Volatile
Bull      [ 0.95,   0.03,    0.02 ]
Bear      [ 0.04,   0.93,    0.03 ]
Volatile  [ 0.05,   0.05,    0.90 ]
```

Regimes are **sticky** — once you're in a bull market, there's a 95% chance it continues tomorrow. This matches observed real-world behaviour: trends persist, volatility clusters.

### 3. Sampling logic

Each day, the model draws a random number and finds which regime it lands in based on cumulative transition probabilities:

```js
function sampleRegime(current, rng) {
  let cumulative = 0;
  const r = rng(); // random 0–1
  for (let j = 0; j < 3; j++) {
    cumulative += trans[current][j];
    if (r < cumulative) return j; // first threshold crossed wins
  }
}
```

---

## Controls

| Slider | What it does |
|--------|-------------|
| **Trading days** | Length of simulation. 252 = one trading year. |
| **Volatility scale** | Amplifies daily shocks. Set high to simulate a crisis period. |
| **Random seed** | Each seed gives a different but fully reproducible price path. |

---

## Stack

- Vanilla JavaScript — no frameworks
- [Chart.js](https://www.chartjs.org/) — price chart rendering
- HTML/CSS — single self-contained file

---

## Run locally

No build step. Just open `index.html` in any browser.

```bash
git clone https://github.com/manitbhasin6-commits/market-regime-detection
cd market-regime-detection
open index.html
```

---

Built by [Manit Bhasin](https://github.com/manitbhasin6-commits)
