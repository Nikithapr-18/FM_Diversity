# Experimental Study of Spatial Diversity Using Dual RTL-SDR for FM Signals

Characterizes spatial diversity for a commercial FM broadcast signal (93.5 MHz) by capturing envelope power simultaneously on two spatially separated RTL-SDR receivers, then applying and evaluating diversity-combining techniques.

## Method

**Capture (`FM_Diversity.grc` — GNU Radio):**
1. Two RTL-SDR dongles capture complex IQ simultaneously at 2.4 MSPS, tuned to 93.5 MHz
2. Each branch decimated 100× → 24 kSPS
3. Instantaneous power `|x[n]|²` computed independently per branch
4. Moving-average smoothing to isolate the fading envelope
5. Logged to raw `float32` `.bin` files, one per branch

Branch 0 (SDR0) was placed indoors, Branch 1 (SDR1) near a window, ~1.5–2 m apart, to promote spatial decorrelation. Both receivers were held static for a single ~96 s simultaneous run (gain = 40 for Branch 0, 30 for Branch 1), plus short dead-frequency noise captures per branch for SNR reference.

**Analysis (`FM_Diversity.ipynb`):**
- Load, trim, and clock-align both branches; correct for the 10 dB gain offset between them
- Selection Combining (SC) and Equal Gain Combining (EGC)
- 1% outage threshold and diversity gain (measured vs. theoretical i.i.d. Rayleigh)
- Branch correlation, SNR margin vs. noise floor
- Level Crossing Rate (LCR) and Average Fade Duration (AFD)

## Key Results
- Branch correlation ρ ≈ 0.274 — decorrelated but not independent
- Diversity gain: SC = 0.00 dB, EGC = 3.55 dB (vs. theoretical Rayleigh: 7.51 dB and 11.39 dB)
- SC gains nothing because Branch 1 (window) stays stronger than Branch 0 even at its own worst moments — no crossover
- EGC still delivers real gain since it coherently combines both branches every sample rather than switching
- SNR margin: 6.55 dB (Branch 0, indoor) vs. 12.89 dB (Branch 1, window), consistent with indoor penetration loss

**Conclusion:** low-cost dual RTL-SDR diversity reception shows a measurable benefit only with EGC; SC gave no gain because one branch dominated throughout. The shortfall from Rayleigh theory is mainly explained by branch correlation and slow, static-receiver fading.

*Note: EGC is an idealized, perfectly co-phased upper bound (only power, not phase, was captured), and results come from a single ~96 s capture in one environment.*

## Requirements

Hardware:
- Two RTL-SDR dongles (RTL2832U-based), one per diversity branch
- Antennas suitable for the FM broadcast band

Software:
- GNU Radio (for capture, `FM_Diversity.grc`)
- Python: numpy, matplotlib, scipy
