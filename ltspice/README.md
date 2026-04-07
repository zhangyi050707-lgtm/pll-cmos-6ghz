# LTspice Files

## Setup
1. Place `tsmc018.lib` in the same directory as `pll_final.asc`
2. Open `pll_final.asc` in LTspice 26.0.1 or later

## Final Configuration
- R_lf = 500 Ω
- C_lf1 = 50 pF, C_lf2 = 5 pF
- Transistor-level comparator disconnected
- Default initial condition: V(out) = 0.5 V (worst-case, VCO dead zone)

---

## Open-Loop Test: VCO Tuning Curve

**Schematic changes required before running:**
1. Connect Vctrl1 to the "out" node (move wire from N001)
2. Disconnect R_lf from "out" (break the loop) to prevent CP from interfering with Vctrl

**After test:** reconnect R_lf and move Vctrl1 back.

```spice
.step param VC 0 2 0.05
.tran 0 300n 0 5p
.ic V(OUTP)=1
.param Icomp_val=0

.meas tran T1 WHEN V(OUTP)=0.95 RISE=100
.meas tran T2 WHEN V(OUTP)=0.95 RISE=101
.meas tran Tper PARAM T2-T1
.meas tran Fvco PARAM 1/Tper

.include tsmc018.lib
```

---

## Closed-Loop Test: Standard Measurement

Configuration: R_lf = 500 Ω, V3 = 781.25 MHz, comparator disconnected.

```spice
.tran 0 30u 0 50p
.ic V(OUTP)=1
.ic V(out)=0.5
.param Icomp_val=0

* --- Vctrl steady-state ---
.meas tran Vctrl_avg AVG V(out) FROM=27u TO=30u
.meas tran Vctrl_max MAX V(out) FROM=27u TO=30u
.meas tran Vctrl_min MIN V(out) FROM=27u TO=30u
.meas tran Vrip_pp PARAM Vctrl_max-Vctrl_min

* --- UP / DN ---
.meas tran UP_avg AVG V(up) FROM=27u TO=30u
.meas tran DN_avg AVG V(dn) FROM=27u TO=30u
.meas tran UPDN_ratio PARAM UP_avg/DN_avg

* --- Lock time ---
.meas tran Tlock_5pct WHEN V(out)=1.49 RISE=LAST
.meas tran Tlock_2pct WHEN V(out)=1.54 RISE=LAST

* --- Overshoot ---
.meas tran Vout_final AVG V(out) FROM=29u TO=30u
.meas tran Vout_pk MAX V(out) FROM=0 TO=30u
.meas tran Overshoot_pct PARAM 100*(Vout_pk-Vout_final)/Vout_final

* --- VCO frequency ---
.meas tran Tvco1 WHEN V(OUTP)=0.95 RISE=167000
.meas tran Tvco2 WHEN V(OUTP)=0.95 RISE=167001
.meas tran Tvco PARAM Tvco2-Tvco1
.meas tran Fvco_meas PARAM 1/Tvco
.meas tran Fvco_err_pct PARAM 100*(Fvco_meas-6.25G)/6.25G

* --- Divider frequency ---
.meas tran Tdiv1 WHEN V(qdiv8)=0.5 RISE=21000
.meas tran Tdiv2 WHEN V(qdiv8)=0.5 RISE=21001
.meas tran Tdiv PARAM Tdiv2-Tdiv1
.meas tran Fdiv_meas PARAM 1/Tdiv
.meas tran DIV_ratio PARAM Fvco_meas/Fdiv_meas

* --- Phase error ---
.meas tran T_ref_28u WHEN V(ref)=0.5 RISE=1 TD=28u
.meas tran T_div_28u WHEN V(qdiv8)=0.5 RISE=1 TD=28u
.meas tran Dt_phase_28u PARAM T_div_28u-T_ref_28u
.meas tran Phase_err_28u PARAM 360*Dt_phase_28u*781.25e6

.meas tran T_ref_29u WHEN V(ref)=0.5 RISE=1 TD=29u
.meas tran T_div_29u WHEN V(qdiv8)=0.5 RISE=1 TD=29u
.meas tran Dt_phase_29u PARAM T_div_29u-T_ref_29u
.meas tran Phase_err_29u PARAM 360*Dt_phase_29u*781.25e6

* --- Reference spur ---
.meas tran Fspur PARAM Vrip_pp*540e6
.meas tran Spur_dBc PARAM 20*log10(Fspur/(2*Fvco_meas))

* --- Power ---
.meas tran Ivdd AVG I(V2) FROM=27u TO=30u
.meas tran Ivdd_cp AVG I(VDD_2) FROM=27u TO=30u
.meas tran P_vdd PARAM -Ivdd*1
.meas tran P_vddcp PARAM -Ivdd_cp*2
.meas tran P_total PARAM P_vdd+P_vddcp

.include tsmc018.lib
.param VC=1.7
```
