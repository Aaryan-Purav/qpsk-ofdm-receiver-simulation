# QPSK/OFDM Communication System Simulator

A complete end-to-end software simulation of a QPSK baseband receiver for mobile communications, built as part of the Mobile Communication Systems course at Vidyalankar Institute of Technology, Mumbai.

---

## Authors

- **Aaryan Purav** — [aaryan.purav@vit.edu.in](mailto:aaryan.purav@vit.edu.in)
- **Shailesh Pawar** — [shailesh.pawar@vit.edu.in](mailto:shailesh.pawar@vit.edu.in)

**Guide:** Prof. Swapnil Ashtekar, Department of Electronics and Telecommunication, VIT Mumbai

---

## What This Project Does

This simulator models the complete digital communications chain that you would find in a real 4G/LTE receiver — from raw bits at the transmitter all the way to BER measurement at the receiver. Every block is implemented from scratch in Python.

The system covers:

- Gray-coded QPSK modulation and demodulation
- Root Raised Cosine (RRC) pulse shaping and matched filtering (β = 0.35)
- Realistic wireless channel models: AWGN, Rayleigh fading, and 3GPP LTE profiles (EPA, EVA, ETU)
- OFDM processing with 64 subcarriers, 16-sample cyclic prefix, and pilot-based channel estimation
- Channel equalization: Zero-Forcing, MMSE, LMS Adaptive, and Decision-Feedback
- Carrier frequency offset estimation and correction (M-th power + Costas loop)
- Symbol timing recovery (Mueller & Müller algorithm)
- BER vs Eb/N0 analysis compared against theoretical QPSK predictions
- Real-time PyQt5 GUI with Matplotlib visualizations

---

## System Architecture

```
TRANSMITTER
───────────────────────────────────────────────────────
Bit Generator → Gray Code Mapper → QPSK Symbol Mapper
→ Upsampler (×4) → RRC Filter (β=0.35)
→ OFDM Tx [Pilot Insertion → IFFT (N=64) → Add CP (L=16)]

CHANNEL
───────────────────────────────────────────────────────
Multipath Convolution (EPA / EVA / ETU / Rayleigh)
→ AWGN Addition

RECEIVER
───────────────────────────────────────────────────────
CFO Estimation → CFO Correction
→ OFDM Rx [Remove CP → FFT → LS+MMSE Channel Estimation → Per-subcarrier EQ]
→ Time-Domain Equalizer (ZF / MMSE / LMS / DFE)
→ RRC Matched Filter → M&M Timing Recovery → Costas Loop
→ Hard Decision → Gray Decoder → Bit Output

ANALYSIS
───────────────────────────────────────────────────────
BER Calculator → Performance Analyzer
```

---

## Key Parameters

| Parameter | Value |
|---|---|
| Modulation | QPSK (Gray-coded) |
| Samples per symbol | 4 |
| RRC roll-off factor β | 0.35 |
| RRC filter span | 8 symbols |
| OFDM subcarriers N | 64 |
| Cyclic prefix length | 16 samples |
| Pilot subcarrier spacing | 4 |
| Channel estimation | LS + MMSE |
| LTE channel models | EPA, EVA, ETU |
| Equalizers | ZF, MMSE, LMS, DFE |

---

## Project Structure

```
QPSK_Simulator/
│
├── main.py                 # GUI application and simulation orchestration
├── qpsk_modem.py           # QPSK modulator and demodulator
├── channel_models.py       # AWGN, LTE EPA/EVA/ETU, Rayleigh fading
├── ofdm_engine.py          # OFDM modulation, demodulation, channel estimation
├── equalization.py         # ZF, MMSE, LMS, DFE equalizers
├── synchronization.py      # Costas loop, M&M timing, CFO estimation
├── channel_estimation.py   # LS and MMSE channel estimation helpers
├── filters.py              # RRC pulse shaping filter design
├── analysis.py             # BER calculation and theoretical curves
├── visualizations.py       # Matplotlib plot helpers
└── requirements.txt        # Python dependencies
```

---

## Installation

**Requirements:** Python 3.8 or higher

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/qpsk-ofdm-receiver-simulation.git
cd qpsk-ofdm-receiver-simulation

# Install dependencies
pip install -r requirements.txt

# Run the simulator
python QPSK_Simulator/main.py
```

**Dependencies** (from `requirements.txt`):
```
numpy>=1.24.0
matplotlib>=3.7.0
scipy>=1.10.0
PyQt5>=5.15.0
```

---

## GUI Overview

The simulator launches a desktop GUI with the following tabs:

| Tab | What it shows |
|---|---|
| Constellation | QPSK received symbol scatter plot with ideal reference points |
| Eye Diagram | I and Q component eye diagrams after matched filtering |
| BER Performance | BER vs Eb/N0 curve (simulated vs theoretical) |
| Spectrum | Power Spectral Density of the transmitted signal |
| Multipath Channel | Channel impulse response and frequency response |
| Equalization | Constellation before and after equalization |
| Synchronization | Costas loop phase error and M&M timing error |
| OFDM System | Channel estimate magnitude/phase and equalized constellation |
| Results | Numeric summary of all simulation metrics |

**Control panel parameters you can adjust:**
- Number of bits (100 to 100,000)
- SNR in dB (-5 to 30 dB)
- Channel model (AWGN, LTE EPA, LTE EVA, LTE ETU, Rayleigh)
- Equalizer type (None, Zero-Forcing, MMSE, LMS, DFE)

---

## Results Summary

Under AWGN conditions the simulated BER matches the theoretical QPSK curve:

$$\text{BER} = \frac{1}{2} \, \text{erfc}\!\left(\sqrt{\frac{E_b}{N_0}}\right)$$

| Channel / Equalizer | Eb/N0 = 10 dB | Notes |
|---|---|---|
| AWGN (no EQ) | ~7.9 × 10⁻⁴ | Matches theory |
| LTE EPA + ZF | ~3.2 × 10⁻³ | Residual ISI from multipath |
| LTE EPA + MMSE | ~1.8 × 10⁻³ | Better than ZF at low SNR |
| Rayleigh (no EQ) | ~5.1 × 10⁻³ | Deep fades degrade performance |
| AWGN + OFDM | ~8.1 × 10⁻⁴ | Consistent with single-carrier |

MMSE equalization consistently outperforms Zero-Forcing under multipath by avoiding noise amplification at low-power channel taps. OFDM with pilot-based LS+MMSE estimation recovers clean constellation quality even under LTE EPA fading at 15 dB.

---

## References

1. J. G. Proakis and M. Salehi, *Digital Communications*, 5th ed. McGraw-Hill, 2008.
2. 3GPP TS 36.101, *Evolved Universal Terrestrial Radio Access (E-UTRA); UE Radio Transmission and Reception*, Release 17, 2022.
3. S. Haykin, *Communication Systems*, 4th ed. John Wiley & Sons, 2001.
4. F. M. Gardner, "Interpolation in Digital Modems — Part I: Fundamentals," *IEEE Trans. Commun.*, vol. 41, no. 3, pp. 501–507, Mar. 1993.

---

## License

This project was developed for academic purposes as part of coursework at Vidyalankar Institute of Technology. Feel free to use it for learning and reference.
