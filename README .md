# LiDAR Wind Field Forecasting

When does Taylor's Frozen Turbulence Hypothesis (TFH) actually work for LiDAR-based wind forecasting, and when does it break? This project answers that with a parameter sweep over turbulence intensity and measurement distance, plus a sanity check against two real wind farm datasets.

## Background

Wind turbines have a control problem: gusts hit the rotor before the controller knows they're coming. By the time the pitch system reacts, the load has already happened. Upstream LiDAR is the way around this. You measure the wind a few hundred meters ahead, predict what arrives at the rotor, and adjust pitch in advance.

The standard way to do that prediction is TFH. It assumes wind structures travel downstream without evolving, so whatever the LiDAR sees at distance x reaches the rotor after time x/U. The assumption is clean and easy to implement, and it gets worse as x grows.

In real flow, turbulent eddies decorrelate as they advect. Small eddies break up fast; large ones hold together longer. So TFH should be fine for low-frequency content over short distances, and should fall apart as distance and turbulence intensity grow. The question is where exactly that line falls, and whether it sits inside or outside the operating range used for feedforward control. That's what this project tries to pin down.

## Methodology

I generate synthetic wind from a Kaimal spectrum (IEC 61400-1) at three turbulence intensities meant to represent different atmospheric conditions:

| Condition | TI | Description |
| --- | --- | --- |
| Stable | ~5% | Night-time, stratified flow |
| Neutral | ~15% | Overcast, moderate mixing |
| Unstable | ~30% | Daytime, strong thermal convection |

To build the downstream signal I apply the Davenport coherence function:

```
γ = exp(-decay × f × x / U)
```

where f is frequency, x is separation distance, and U is mean wind speed. TFH ignores this decorrelation. Davenport puts the frequency-dependent decay back in as a simple analytical form, so the downstream signal is no longer a perfect time-shifted copy of the upstream one.

The full sweep covers 5 turbulence intensities × 5 separation distances × 20 realisations per case, so 500 simulations total. Error metrics are RMSE and Pearson correlation between the upstream prediction and the downstream "truth".

For real-world checks I use two open wind farm datasets:

- **UEBB** (Beberibe, Brazil): concurrent LiDAR and met mast at 50–300 m separations. Used for longitudinal coherence.
- **RW-Turb** (Pays d'Othe, France): 100 Hz sonic anemometer at two heights. Used for power spectral density comparison against the Kaimal model.

## Key Results

### Stability comparison

[![Stability Comparison](https://github.com/qama94/lidar-wind-forecasting/raw/main/results/stability_comparison.png)](/qama94/lidar-wind-forecasting/blob/main/results/stability_comparison.png)

| Condition | TI (%) | RMSE (m/s) | Correlation (r) | Validity |
| --- | --- | --- | --- | --- |
| Stable | 5.0 | 0.419 | 0.655 | Good |
| Neutral | 14.9 | 1.524 | 0.491 | Moderate |
| Unstable | 29.8 | 3.827 | 0.194 | Poor |

Going from stable to unstable conditions, correlation drops from 0.66 to 0.19 and RMSE goes up by almost an order of magnitude. So TFH-based forecasting works in stable conditions and is essentially unusable in unstable ones.

### Parameter sweep

[![Parameter Sweep Heatmap](https://github.com/qama94/lidar-wind-forecasting/raw/main/results/parameter_sweep_heatmap.png)](/qama94/lidar-wind-forecasting/blob/main/results/parameter_sweep_heatmap.png)

The sweep shows correlation dropping from r ≈ 0.51 at 50 m / 5% TI to r ≈ 0.12 at 300 m / 20% TI. Distance matters more than turbulence intensity in this range. Past about 100 m separation, correlation drops below useful regardless of TI. Commercial nacelle LiDARs typically measure further out than that, so the breakdown sits inside the operating range that matters for control.

### Real data validation

**Power spectral density (RW-Turb 100 Hz sonic vs Kaimal):**
[![PSD Comparison](https://github.com/qama94/lidar-wind-forecasting/raw/main/results/real_data_100Hz_psd_comparison.png)](/qama94/lidar-wind-forecasting/blob/main/results/real_data_100Hz_psd_comparison.png)

The real data follows the expected −5/3 inertial subrange slope, so the Kaimal model is a fair stand-in for the synthetic wind generation step.

**Longitudinal coherence (UEBB LiDAR vs met mast vs Davenport):**
[![LiDAR Coherence](https://github.com/qama94/lidar-wind-forecasting/raw/main/results/real_data_lidar_coherence.png)](/qama94/lidar-wind-forecasting/blob/main/results/real_data_lidar_coherence.png)

Real coherence decays from ~1.0 at low frequencies to ~0 at high frequencies, which is the shape Davenport predicts. But the model overestimates coherence at higher frequencies, meaning the standard decay parameter (8.0) is too low for this coastal site. Real decorrelation happens faster than the textbook value suggests.

## Setup parameters

```
Mean wind speed:      10.0 m/s
Measurement height:   80.0 m
Separation distances: 50, 100, 150, 200, 300 m
TI values:            5%, 10%, 15%, 20%, 25%
Signal duration:      600 s
Time resolution:      0.1 s
Realisations:         20 per case
```

## Repository structure

```
lidar-wind-forecasting/
├── 01_lidar_wind_forecasting.ipynb  # Main analysis notebook
├── data/                            # Downloaded datasets (not tracked)
├── results/
│   ├── stability_comparison.png
│   ├── correlation_vs_ti.png
│   ├── power_spectra.png
│   ├── parameter_sweep_heatmap.png
│   ├── real_data_100Hz_psd_comparison.png
│   └── real_data_lidar_coherence.png
└── README.md
```

## Datasets

- **UEBB**: Passos et al. (2017). Coastal operating wind farms: two datasets with concurrent SCADA, LiDAR and turbulent fluxes. Zenodo. https://doi.org/10.5281/zenodo.1475197
- **RW-Turb**: Gires et al. (2022). Combined high-resolution rainfall and wind data collected for 3 months at a wind farm. Zenodo. https://doi.org/10.5281/zenodo.5801900

## Installation

```
git clone https://github.com/qama94/lidar-wind-forecasting.git
cd lidar-wind-forecasting
pip install -r requirements.txt
jupyter notebook
```

## References

- Kaimal et al. (1972). Spectral characteristics of surface-layer turbulence. QJRMS.
- Taylor, G.I. (1938). The spectrum of turbulence. Proceedings of the Royal Society A.
- Davenport, A.G. (1961). The spectrum of horizontal gustiness near the ground. QJRMS.
- Schlipf et al. (2010). Lidar-assisted feedforward wind turbine control. EWEC.
- IEC 61400-1 (2019). Wind energy generation systems. Design requirements.

## Author

Gamar Ismayilova
[linkedin.com/in/gamar-ismayilova](https://linkedin.com/in/gamar-ismayilova) | gamar.ismayilova@gmail.com
