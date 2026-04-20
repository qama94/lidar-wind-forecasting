\# LiDAR Wind Field Forecasting



Parametric study of Taylor's Frozen Turbulence Hypothesis validity for LiDAR-based wind field forecasting under different atmospheric stability conditions.



\## Research Question



How does atmospheric stability affect the validity of Taylor's Frozen Turbulence Hypothesis for short-term wind speed forecasting using upstream LiDAR measurements?



Taylor's Frozen Turbulence Hypothesis assumes that wind turbulence patterns are frozen in space and advected downstream at the mean wind speed. This is the core assumption behind LiDAR-based predictive wind control for turbines. The hypothesis works well under low turbulence conditions but breaks down when the atmosphere is unstable and turbulence intensity is high.



\## Methodology



\### Synthetic Wind Generation

Wind time series are generated using the Kaimal spectral model — the standard IEC 61400-1 model for atmospheric turbulence. Three atmospheric stability conditions are simulated by varying turbulence intensity:



| Condition | Turbulence Intensity | Description |

|-----------|---------------------|-------------|

| Stable    | \~5%                 | Night-time, clear sky, stratified flow |

| Neutral   | \~15%                | Overcast, moderate mixing |

| Unstable  | \~30%                | Daytime, strong thermal convection |



\### Taylor's Hypothesis Implementation

For a separation distance x between upstream LiDAR and downstream turbine, the predicted downstream wind speed is:



&#x20;   u\_predicted(t) = u\_upstream(t - x/U)



where U is the mean wind speed.



\### Validation Metrics

\- RMSE: Root Mean Square Error between predicted and actual downstream wind speed

\- Correlation coefficient (r): linear correlation between predicted and actual signal

\- Turbulence Intensity (TI): standard deviation divided by mean wind speed



\## Results



| Condition | TI (%) | RMSE (m/s) | Correlation (r) | Validity  |

|-----------|--------|------------|-----------------|-----------|

| Stable    | 5.0    | 0.419      | 0.655           | Good      |

| Neutral   | 14.9   | 1.524      | 0.491           | Moderate  |

| Unstable  | 29.8   | 3.827      | 0.194           | Poor      |



Taylor's hypothesis performs acceptably under stable conditions but degrades significantly as turbulence increases, becoming unreliable under unstable atmospheric conditions.



\## Setup Parameters



&#x20;   Mean wind speed:      10.0 m/s

&#x20;   Measurement height:   80.0 m

&#x20;   Separation distance:  100.0 m

&#x20;   Time lag:             10.0 s

&#x20;   Signal duration:      600 s

&#x20;   Time resolution:      0.5 s



\## Repository Structure



&#x20;   lidar-wind-forecasting/

&#x20;   notebooks/

&#x20;       01\_data\_exploration.ipynb

&#x20;   results/

&#x20;       stability\_comparison.png

&#x20;       correlation\_vs\_ti.png

&#x20;       power\_spectra.png

&#x20;   requirements.txt

&#x20;   README.md



\## Installation



&#x20;   git clone https://github.com/qama94/lidar-wind-forecasting.git

&#x20;   cd lidar-wind-forecasting

&#x20;   pip install -r requirements.txt

&#x20;   jupyter notebook

s

\## Dependencies



\- numpy

\- scipy

\- matplotlib

\- pandas

\- jupyter



\## References



\- Kaimal et al. (1972). Spectral characteristics of surface-layer turbulence. Quarterly Journal of the Royal Meteorological Society.

\- Taylor, G.I. (1938). The spectrum of turbulence. Proceedings of the Royal Society A.

\- IEC 61400-1 (2019). Wind energy generation systems - Design requirements.



\## Author



Gamar Ismayilova

linkedin.com/in/gamar-ismayilova

