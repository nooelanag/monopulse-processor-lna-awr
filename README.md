# Monopulse Processor with Low-Noise Amplifier (AWR Microwave Office)

Lab project of *Radio Frequency and Antenna Subsystems*
(Master's in Telecommunication Engineering, UC3M).

Design of a complete **monopulse processor** at 2.3 GHz in **microstrip
technology** on FR4, simulated in AWR Microwave Office. A monopulse receiver
combines the signals captured by two radiating elements into a **sum channel**
and a **difference channel**, which together determine the angular position of a
target. Every block is first designed with ideal transmission lines, then
converted to real microstrip, then extended with circuit discontinuities, and
finally laid out.

## Subsystems

### Wilkinson divider

Two quarter-wavelength lines of characteristic impedance close to sqrt(2) * Z0,
a 100 ohm isolation resistor between the outputs and 50 ohm ports.

| Model | S11 at 2.3 GHz | S21 / S31 | Isolation |
|---|---|---|---|
| Ideal (`TLIN`) | matched | about -3 dB | high |
| Real microstrip (`MLIN`) | -48.61 dB | about -3.1 dB | about -48 dB |
| Real with circuit elements | -37.39 dB | -3.184 dB | - |

The excess over the theoretical -3 dB comes from the conductor and dielectric
losses of the FR4 substrate.

### Rat-race hybrid

Four-port ring with three sections of 90 degrees of electrical length and one of
270 degrees. Fed from two adjacent ports, it delivers the sum of the two inputs
at the sigma port and their difference at the delta port.

| Model | Reflection at all four ports (2.3 GHz) |
|---|---|
| Real with circuit elements | about -46.84 dB |

### Phase-shifting line

The two radiating elements are spaced d = 0.7 wavelengths apart. For a wavefront
arriving at 20 degrees from the array normal, the path difference is
d * sin(theta), which corresponds to a phase difference of

    360 * 0.7 * sin(20 degrees) = 86.18 degrees, rounded to 86 degrees

The line is inserted in the path of one element so that both signals reach the
processor in phase for a source at 20 degrees. It is implemented first as an
ideal `TLIN` of 86 degrees and then as a 50 ohm `MLIN` dimensioned with TXLine.

### Low-noise amplifier

Active device: **ATF-36163** transistor.

- Bias point chosen among four candidates as the one with the lowest noise figure
  at the design frequency.
- Stability study from the input and output stability circles: the transistor is
  conditionally stable, since both circles intersect the Smith chart. The stable
  region lies outside the input circle and inside the output circle.
- Source impedance Zs taken on the minimum-noise circle located inside the stable
  region, giving a conjugate input match. Input and output networks synthesised
  with the line-plus-stub technique using 50 ohm lines.
- DC bias: Id = 10 mA and Vds = 2 V. With a 100 ohm drain resistor this gives a
  3 V supply. The gate network is an RC low-pass that presents about 10 kohm at
  the working frequency, so it barely loads the input matching network. The drain
  network is a quarter-wavelength 50 ohm line with a decoupling capacitor to
  ground, which looks like an open circuit at RF while setting Vds in DC.

Targets: gain above 10 dB, noise figure between 0.5 and 1 dB, output matching
below -10 dB and reverse isolation below -10 dB.

| Model | S21 (gain) | S11 | S22 | Noise figure |
|---|---|---|---|---|
| Ideal RF network | about 16.6 dB | about -20 dB | about -4.8 dB | about 0.87 dB |
| Real, with bias networks and discontinuities | about 16 dB | degraded | degraded | slightly higher |

The bias networks proved effectively transparent at RF: gain and noise figure
stay practically unchanged with respect to the model without them.

## Full processor

The individual layouts of the Wilkinson divider, the phase-shifting line, the two
low-noise amplifiers and the rat-race hybrid are assembled on a single FR4
substrate. The real schematic adds the electromagnetic discontinuities of any
physical implementation, modelled with `MTEE` for T-junctions, `MBEND` for
90-degree corners and `MSTEP` for width changes, plus 0402 surface-mount
resistors and capacitors. The 0402 format was preferred over 0805 or 1206 to
reduce parasitic inductance and capacitance at microwave frequencies.

Verification is run both with the nominal phase-shifting line and with
practically zero phase shift. For the zero-shift case S21 approaches the
amplifier gain, the noise figure of the chain stays around 1 dB and S31 shows
more than 10 dB of attenuation.

### Bandwidth

The bandwidth of the processor is set by its most restrictive subsystem. The
Wilkinson divider and the rat-race hybrid are resonant structures with fractional
bandwidths typically in the 10 to 30 per cent range. The effective system
bandwidth is the frequency range where input matching stays below -10 dB, gain
does not drop more than 3 dB from its maximum, the noise figure stays within
acceptable limits and the sum and difference ports remain adequately isolated.

### Layout constraints

All layouts respect the fabrication limits given in the lab statement, with line
widths and conductor spacings above the specified minimum of 0.15 mm. The layout
also minimises ground connection lengths to reduce parasitic inductance and keeps
lines apart to avoid unwanted coupling.

## Repository contents

- `PROYECTO.emp` - AWR Microwave Office project with every subsystem and layout
- `MEMORIA.pdf` - lab report, in Spanish

## How to run

Open the `.emp` project in AWR Microwave Office (AWR Design Environment) and run
the linear, noise and layout analyses. Simulation data sets are not included,
since AWR regenerates them locally on the first run.

## Notes

The lab statement and the base material were provided by the course instructors
and are not included in this repository.

## Authors

[Noel Andolz Aguado](https://github.com/nooelanag) and
[Daniel Lozano Uceda](https://github.com/dalouc)
