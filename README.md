# StreamLedger

**An event-sourced, CQRS-projected ledger service for balances and inventory - built so concurrent writes can never produce a double-spend or a lost update.**

![StreamLedger](assets/hero.png)

[![CI](https://github.com/rushikiranadiboina/streamledger/actions/workflows/ci.yml/badge.svg)](https://github.com/rushikiranadiboina/streamledger/actions/workflows/ci.yml)
[![Go Report](https://img.shields.io/badge/go-1.25-00ADD8?logo=go)](go.mod)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## The problem

Fintech and marketplace backends live and die by one property: **the ledger must be right, even when a thousand requests hit the same account at once.** The naive approach - "SELECT balance ... ; UPDATE balance = balance - amount" - is a textbook race condition. Two concurrent withdrawals can both read the same balance, both decide there's enough money, and both commit: a double-spend. Retrying a timed-out payment can silently double-charge a customer. And when something does go wrong, "what actually happened to this account" is only ever a lossy UPDATE log away, if it's logged at all.

StreamLedger solves this with three techniques used in production financial systems, wired together end to end rather than described in a slide deck