# LTspice Files

## Setup
1. Place `tsmc018.lib` in the same directory as `pll_final.asc`
2. Open `pll_final.asc` in LTspice 26.0.1 or later

## Simulation Commands

| Test | Command |
|------|---------|
| Standard lock | `.tran 0 30u 0 50p` |
| Extended stability | `.tran 0 50u 0 50p` |
| High-precision jitter | `.tran 0 30u 0 5p` |

## Initial Condition
Default: `V(out) = 0.5 V` (worst-case, VCO dead zone)

## Final Configuration
- R_lf = 500 Ω
- C_lf1 = 50 pF, C_lf2 = 5 pF
- Transistor-level comparator disconnected
