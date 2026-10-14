# Geometric Aspect-Ratio Optimisation

## 2026-08

- [[paper-vault/papers/vanfraassen2022optimisation]]
  - Title: Optimisation of geometric aspect ratio of thin film transistors for low-cost flexible CMOS inverters and its practical implementation
  - Role in paper: Primary design rule for selecting the weaker transistor's `W/L` in a complementary TFT pair.
  - Mechanism: Balances normalized maximum-current mismatch against minimum-current mismatch to maximize the switching-current-to-static-current ratio, then checks noise margin and speed.
  - Why listed here: Replaces silicon-style inverse-mobility sizing with a closed-form rule that explicitly accounts for geometry-dependent leakage.
