# Millennium AmplifyMe Simulation

This repository contains a complete quantitative trading simulation developed as part of the Millennium AmplifyMe program. The project walks through four progressive challenges that build a realistic market‑making and arbitrage system.

## Overview

The simulation replicates the workflow of a systematic market maker:

- **Challenge 1 – Basic Price Making**  
  Create a two‑sided quote (bid & offer) with a fixed 2% spread around the current market price.  
  Learn how to handle client requests without knowing their intended direction.

- **Challenge 2 – Risk‑Based Skewing**  
  Adjust your quotes dynamically based on your current inventory.  
  - **Long position** → shift both prices **down** to encourage selling.  
  - **Short position** → shift both prices **up** to encourage buying.  

- **Beta Research & Hedging**  
  Compute historical beta for five FAANG stocks relative to a synthetic FAANG ETF using covariance/variance.  
  The calculated betas replace a naive 1:1 hedge with a volatility‑adjusted hedge.

- **Challenge 3 – Optimised ETF Hedging**  
  After each executed trade, automatically place a beta‑weighted hedge in the FAANG ETF using the `Exchange` module.  
  The goal is to keep net portfolio risk close to zero.

- **Arbitrage Research & Challenge 4 – Statistical Arbitrage**  
  Identify temporary mispricings between the real FAANG ETF and a synthetic ETF built from the five underlying stocks.  
  Execute a 6‑leg arbitrage trade (ETF + 5 stocks) when the spread exceeds a threshold, and close the position upon convergence.

## Key Components

### Data Feeds
- `prices` – historical price series for each asset (ticker, date, price).  
- `price_requests` – incoming client trade requests (ticker, date, volume).

### Core Classes
- `QuotedTrade` – container for a quote sent to a client (includes bid/offer, reference price, volume, date).  
- `CompletedTrade` – log of a successfully executed trade (includes traded price, side, commission).  
- `ExchangeTrade` – order object used to hedge with the ETF.

### Market Maker Object (`mm`)
- Tracks current positions (`current_positions`).  
- Maintains logs of quoted and completed trades.  
- Provides methods `add_quoted_trade()` and `add_trade()`.

### Hedge Fund Client (`hf`)
- Simulates client responses (`buy`, `sell`, `refuse`) via `hf.show(quote)`.  
- Manages cash balance, positions, and commission costs.

### Exchange Module
- Executes ETF hedge orders via `Exchange.execute(trade)`.  
- Updates the market maker’s ETF position through `mm.update_ETF_position()`.

## Simulation Workflow

1. **Load data** – obtain price series and client requests.
2. **For each request**:
   - Look up the reference price.
   - Check current inventory → compute skew.
   - Generate skewed bid/offer.
   - Log the quote.
   - Send quote to client and capture decision.
   - If accepted:
     - Log the completed trade.
     - Calculate required ETF hedge using the stock’s beta.
     - Execute ETF hedge via the exchange.
     - Log the hedge.

## Visualisation Tasks

- Plot bid/offer and reference price for a chosen ticker (e.g., AAPL) over time.  
- Display the total net nominal risk of the portfolio across the simulation (with a zero‑line).  
- Chart the real vs synthetic ETF price and the spread with statistical thresholds (mean ± 2σ).

## Learning Outcomes
- Object‑oriented design for trading systems.
- Real‑time inventory risk management via price skewing.
- Beta calculation and its use in dynamic hedging.
- Pairs trading / statistical arbitrage detection.
- Visualisation of trading performance and risk.

## Notes
The AmplifyQuantTrading library is a simulation‑only package; it does not connect to live markets.

All commissions and transaction costs are handled internally by the hf object.

The arbitrage strategy assumes zero latency and perfect execution, for educational purposes only.
