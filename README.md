# ECG Cardiac Vector Animation

A real-time animation of how the heart's electrical dipole vector sweeps
through space during one heartbeat — and how that moving vector projects
onto the three Einthoven leads to produce the ECG waveform you see on a monitor.

---

## What This Shows

Every heartbeat is driven by a wave of electrical activation spreading through
the heart muscle. At any instant, all those tiny electrical dipoles sum into a
single resultant vector — the **cardiac vector**. As activation moves from the
atria → ventricles → repolarisation, this vector rotates and changes magnitude.

An ECG lead is simply a **projection** of that rotating vector onto a fixed
direction in the frontal plane. Lead II, for example, points roughly from the
right shoulder to the left foot — it captures the component of the cardiac
vector along that axis. The P wave, QRS complex, and T wave you see on an ECG
are just the cardiac vector's projection varying over time as the wave of
activation sweeps through the heart.

This animation shows both simultaneously:
- **Top panel** — the cardiac vector rotating in 2D space, with Lead I, II,
  and III directions shown as dashed reference lines
- **Bottom panel** — the Lead II ECG trace building up in real time as the
  vector sweeps

---

## What the Code Models

### Cardiac Activation — Three Phases

Each phase of the heartbeat is modelled as a Gaussian pulse in time,
with a specific spatial direction representing where the activation front
is travelling:

```
Activation signal:  A(t) = amplitude × exp(−(t_mod − centre)² / 2σ²)
Cardiac vector:     E(t) = A_atria(t)·d_atria + A_vent(t)·d_vent + A_repol(t)·d_repol
```

| Phase | Centre (s) | Width σ | Amplitude | Direction | ECG feature |
|---|---|---|---|---|---|
| Atrial depolarisation | 0.20 | 0.03 | +0.3 | (0.5, 1.0) | P wave |
| Ventricular Q | 0.45 | 0.01 | −0.3 | (1.0, 0.2) | Q deflection |
| Ventricular R | 0.50 | 0.02 | +1.5 | (1.0, 0.2) | R peak |
| Ventricular S | 0.55 | 0.015 | −0.6 | (1.0, 0.2) | S deflection |
| Repolarisation | 0.75 | 0.05 | +0.4 | (0.6, 0.4) | T wave |

The Q, R, S pulses share the same spatial direction (ventricular activation
travels roughly left and downward) but have opposite amplitudes — Q and S
are negative, R is the dominant positive spike. Their sum produces the
characteristic QRS morphology.

### Lead Projections — Einthoven's Triangle

The three standard limb leads sit 60° apart in the frontal plane:

```
Lead I   = (1.0,  0.0)          →  horizontal, left
Lead II  = (0.5,  √3/2)         →  60° below horizontal
Lead III = (−0.5, √3/2)         →  120° below horizontal

Lead voltage = E⃗ · lead_direction⃗  =  Ex·lead_x + Ey·lead_y
```

This is exactly Einthoven's law in vector form. The animation displays Lead II
because it typically has the largest amplitude — the cardiac vector's mean
direction (left and downward) aligns most closely with Lead II's axis.

---

## The Animation

**Top panel — Cardiac Vector:**
The black arrow sweeps through the 2D frontal plane in real time.
Watch how it barely moves during the P wave (atrial activation is small),
then swings sharply during the QRS (large ventricular forces), then settles
during the T wave (repolarisation, broader and lower amplitude).

**Bottom panel — Lead II ECG:**
The green trace builds progressively, one frame per time step.
You can watch exactly which vector position produces each feature:
- Small upward deflection → P wave (atrial activation, small vector)
- Sharp upward spike → R peak (ventricular vector at maximum)
- Return to baseline → ST segment (vector near zero)
- Broad upward bump → T wave (repolarisation)

---

## How to Run

```bash
git clone https://github.com/Shristy-byte/ecg-cardiac-vector-animation.git
cd ecg-cardiac-vector-animation
pip install -r requirements.txt
python ecg_animation.py
```

A matplotlib window opens and plays the animation at 5ms per frame
(effectively real-time for a 1-second heartbeat period).

---

## Project Structure

```
ecg-cardiac-vector-animation/
├── ecg_animation.py     ← full animation script
├── requirements.txt
└── README.md
```

---

## Requirements

```
numpy
matplotlib
```

```bash
pip install -r requirements.txt
```

---

## Connection to the ECG Arrhythmia Project

This script lives inside the
[ecg-arrhythmia-classification](https://github.com/Shristy-byte/ecg-arrhythmia-classification)
repo as a companion visualisation. The arrhythmia classifier operates on
real MIT-BIH ECG beats — this animation shows the underlying physics of
why those beats have the morphology they do, and why a ventricular ectopic
beat looks different from a normal beat (the ventricular activation direction
changes, rotating the cardiac vector differently, projecting a different
shape onto Lead II).

---

## References

- Einthoven W — The string galvanometer and the measurement of the action
  currents of the heart (Nobel Lecture, 1925)
- Malmivuo J, Plonsey R — Bioelectromagnetism: Principles and Applications
  of Bioelectric and Biomagnetic Fields. Oxford University Press, 1995
  (Chapter 15 — Vectorcardiography)
