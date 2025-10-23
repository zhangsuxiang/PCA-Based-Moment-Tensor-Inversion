# PCA-DECOMPOSITION (Python Version)

**Principal Component Analysis of Seismic Waveforms**

Python implementation of the MATLAB code by Václav Vavryčuk

## Overview

This package performs Principal Component Analysis (PCA) decomposition of seismic traces to extract the common wavelet. The method is designed for moment tensor inversion of microearthquakes.

## Reference

Vavryčuk, V., Adamová, P., Doubravová, J., Jakoubková, H., 2017. Moment tensor inversion based on the principal component analysis of waveforms: Method and application to microearthquakes in West Bohemia, Czech Republic, Seismological Research Letters, 88(5), 1303-1315.

## Requirements

```bash
pip install numpy scipy matplotlib scikit-learn
```

## File Structure

```
PCA-DECOMPOSITION/
├── Programs/
│   ├── PCA_wavelet.py              # Main program
│   ├── configuration_PCA.py         # Configuration parameters
│   └── set_window.py                # Window function
├── Data/
│   ├── event_list.txt               # Event parameters
│   └── Events/                      # Event data files (.mat)
├── Output/                          # Output files
└── Figures/                         # Generated figures
```

## Usage

### 1. Prepare Input Data

Create the directory structure:
```bash
mkdir -p ../Data/Events ../Output ../Figures
```

### 2. Create Event List

Create `../Data/event_list.txt` with the following format:
```
# EventID  W1    W2    W3    W4    fr_low  fr_high  ref_station  ref_polarity
event001   0.8   1.2   2.8   3.2   2.0     20.0     STA01       1
event002   0.9   1.3   2.7   3.1   2.0     20.0     STA02       -1
```

Where:
- `EventID`: Name of the event (must match .mat filename)
- `W1, W2`: Window rising edge times (sec)
- `W3, W4`: Window falling edge times (sec)
- `fr_low, fr_high`: Band-pass filter frequencies (Hz)
- `ref_station`: Reference station name
- `ref_polarity`: Polarity at reference station (+1 or -1)

### 3. Prepare Event Data

Each event should have a `.mat` file in `../Data/Events/` containing:
```python
event_data = {
    'stations': [['STA01'], ['STA02'], ...],  # Station names
    'traces': numpy_array,                     # N_points x N_traces
    'sampling_rate': float                     # Sampling interval (sec)
}
```

### 4. Configure Parameters

Edit `configuration_PCA.py` to adjust:
- Time windows for plotting
- Sampling interval
- Integration options
- Filter parameters
- Plot options
- File paths

### 5. Run the Program

```bash
cd Programs
python PCA_wavelet.py
```

## Output Files

For each event, the program generates:

1. **PCA Amplitudes** (`EventID_pca_amplitudes.txt`)
   ```
   STA01 1.234567e-05
   STA02 -2.345678e-05
   ...
   ```

2. **PCA Wavelet** (`EventID_wavelet.txt`)
   ```
   0.000000 0.000000e+00
   0.001000 1.234567e-06
   ...
   ```

3. **Figures** (if enabled)
   - `EventID_data.png`: Original and filtered traces
   - `EventID_pc.png`: Principal components PC1 and PC2
   - `EventID_data_pc.png`: Traces with principal components

## Algorithm Details

### Processing Steps

1. **Data Preprocessing**
   - Upsampling to target sampling rate
   - Remove DC offset (detrend)
   - Optional integration (velocity → displacement)

2. **Band-pass Filtering**
   - Butterworth filter (order 4)
   - Optional zero-phase filtering (filtfilt)

3. **Windowing**
   - Apply taper window to suppress noise
   - Sin² rising edge, cos⁴ falling edge

4. **Two-Step Alignment**
   - First: Align traces to reference station
   - Second: Align to first principal component

5. **PCA Decomposition**
   - Extract principal components
   - Compute PCA amplitudes
   - Normalize and determine polarity

### Key Features

- **Robust to noise**: PCA reduces noise effects
- **Time-shift correction**: Cross-correlation alignment
- **Flexible filtering**: Multiple frequency bands
- **Quality control**: Visual inspection via plots

## Comparison with MATLAB Version

This Python implementation is designed to produce **identical results** to the original MATLAB code:

### Equivalent Functions

| MATLAB                | Python                          |
|-----------------------|---------------------------------|
| `interp()`           | `scipy.interpolate.interp1d()`  |
| `detrend()`          | `scipy.signal.detrend()`        |
| `cumtrapz()`         | `scipy.integrate.cumulative_trapezoid()` |
| `butter()`           | `scipy.signal.butter()`         |
| `filtfilt()`         | `scipy.signal.filtfilt()`       |
| `filter()`           | `scipy.signal.lfilter()`        |
| `xcorr()`            | `scipy.signal.correlate()`      |
| `pca()`              | Custom implementation           |

### Differences

1. **Figure formats**: PNG instead of EPS (easier to view)
2. **PCA implementation**: Custom eigenvalue decomposition (matches MATLAB)
3. **Array indexing**: 0-based (Python) vs 1-based (MATLAB)

## Testing

To verify the implementation:

```bash
# Test window function
python set_window.py

# Test configuration
python configuration_PCA.py

# Run comparison test (if you have MATLAB outputs)
python compare_outputs.py
```

## Troubleshooting

### Common Issues

1. **File not found**
   - Check that paths in `configuration_PCA.py` are correct
   - Ensure event data files exist in `../Data/Events/`

2. **Import errors**
   - Install required packages: `pip install -r requirements.txt`

3. **Different results from MATLAB**
   - Check sampling rate and upsampling
   - Verify filter parameters
   - Compare intermediate outputs

## License

The rights to the software are owned by V. Vavryčuk. The code can be freely used for research purposes. The use of the software for commercial purposes with no commercial license is prohibited.

## Contact

For questions about the method, please refer to the original publication:
- Vavryčuk et al. (2017), Seismological Research Letters

## Version History

- **1.0** (2025): Python port of MATLAB version 1.0 (2017)
  - Complete feature parity with MATLAB version
  - Improved documentation
  - Enhanced visualization

## Acknowledgments

Original MATLAB code: Václav Vavryčuk (Institute of Geophysics, Czech Academy of Sciences)
