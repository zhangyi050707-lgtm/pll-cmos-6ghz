# 6.25 GHz CMOS Charge-Pump PLL — LTspice & MATLAB

This repository contains the complete simulation files for a 6.25 GHz integer-N charge-pump phase-locked loop, designed as a UCL 3rd Year Final Project.

## Project Summary

- **Target frequency**: 6.25 GHz (C-band)
- **Process**: TSMC 0.18 µm CMOS (BSIM3)
- **Simulator**: LTspice 26.0.1 + MATLAB
- **Key finding**: The standard phase-margin formula predicts 56.4°, but the true phase margin is only 4.6°. The circuit locks due to accidental nonlinear stabilisation (varactor dead zone, CP mismatch, Kvco nonlinearity).

## Repository Structure

```
├── ltspice/
│   ├── pll_final.asc          # Main PLL schematic
│   ├── tsmc018.lib            # TSMC 0.18µm process models
│   └── README.md              # LTspice setup instructions
├── matlab/
│   ├── pll_ideal_v2.m         # Generic ideal loop (PM = 60°)
│   ├── pll_noise_v2.m         # Phase noise + IFFT + eye diagram
│   ├── pll_ltspice_matched.m  # LTspice-matched analysis (PM = 4.6°)
│   └── Vout_lock.txt          # Exported LTspice lock transient
├── figures/                   # Generated figures for the report
└── README.md                  # This file
```

## Key Results

| Parameter | Value |
|-----------|-------|
| Lock time (±2%) | 8.43 µs |
| Control voltage ripple | 14.3 mV |
| Reference spur | −64.1 dBc |
| Total power | 1.96 mW |
| Phase margin (true) | 4.56° |
| Phase margin (formula) | 56.4° |

## How to Reproduce

### LTspice
1. Open `ltspice/pll_final.asc` in LTspice
2. Ensure `tsmc018.lib` is in the same directory
3. Run `.tran 0 50u 0 50p`

### MATLAB
1. Run `pll_ideal_v2.m` first to validate the framework
2. Run `pll_noise_v2.m` for phase noise analysis
3. Run `pll_ltspice_matched.m` for the matched comparison (requires `Vout_lock.txt`)

## Author

Yi Zhang — UCL Department of Electronic & Electrical Engineering, 2023

## License

This project is shared for academic and educational purposes.
