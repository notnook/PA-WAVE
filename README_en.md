[简体中文](README.md) | English

# PA-WAVE: Residential Appliance Waveform Dataset v1.0

This project contains voltage and current waveform recordings of common residential appliances. The recordings use the COMTRADE 1999/ASCII format and can be used for non-intrusive load monitoring (NILM), load feature extraction, and comparison of current transformer characteristics. Each load was recorded using both an **electromagnetic current transformer (CT)** and a **Hall-effect current transformer (CT)**. The dataset will continue to be expanded.

## About Us

Nanjing PowerAnt Electrical Technology Co., Ltd. focuses on businesses related to the “New Power System.” Its team members have extensive industry experience, a solid foundation in product R&D, strong capabilities in implementing scientific research projects, and a well-established R&D organization.

The company has accumulated technical expertise in measurement and metering, relay protection, electrical safety, intelligent operation and maintenance, renewable energy forecasting and coordinated control, and other areas. Drawing on its understanding of user needs, industry pain points, and development trends, the company introduces emerging technologies such as quantum information, power electronics, optical communications, signal analysis, and AI to address current challenges faced by users and the industry. It offers strong capabilities in customized solutions and product iteration.

<p align="center">
  <img src="doc/img/powerant.logo.2.png" alt="POWER ANT" width="320">
</p>

## Table of Contents

- [About Us](#about-us)
- [Dataset Overview](#dataset-overview)
- [Covered Appliances](#covered-appliances)
- [Acquisition Conditions](#acquisition-conditions)
- [Directory Structure](#directory-structure)
- [File Format](#file-format)
- [Quick Start](#quick-start)
- [Known Limitations](#known-limitations)
- [License](#license)
- [Citation](#citation)
- [Feedback](#feedback)

## Dataset Overview

The table below summarizes the current dataset. Additional data will be included in future releases.

| Batch | Sensor | Number of recordings | Appliance categories | Total samples | Total duration | Sampling rate | Channels |
|---|---|---:|---:|---:|---:|---:|---|
| Electromagnetic CT | Electromagnetic current transformer | 46 | 8 | 3,119,616 | 243.72 s | 12,800 Hz | AC_V, AC_I |
| Hall CT | Hall-effect current transformer | 46 | 8 | 2,042,624 | 159.58 s | 12,800 Hz | AC_V, AC_I |
| **Total** | | **92** | **8** | **5,162,240** | **403.30 s** | **12,800 Hz** | |

- The duration of each recording ranges from 1.40 to 7.88 s, with a median of 4.44 s.
- At a sampling rate of 12,800 Hz and a 50 Hz mains frequency, there are **256 samples per cycle**.

**Potential Applications**

- **Load identification:** Clean waveforms of individual loads with operating-condition labels, ready for model training and evaluation.
- **Feature engineering:** Steady-state harmonics, power factor, startup/shutdown behavior, and transient behavior during plug insertion and removal.
- **Sensor comparison:** Comparison of electromagnetic CT and Hall CT measurements for the same load under the same operating condition.
- **Education and tool validation:** COMTRADE 1999 parsing, event detection, and clustering.

## Covered Appliances

The current release includes the following appliances and operating conditions. More data will be added in future releases.

| Appliance category | Model | Operating conditions | Electromagnetic CT | Hall CT |
|---|---|---|---:|---:|
| Refrigerator | Midea MR-116M | Normal operation | 1 | 1 |
| Water purifier/dispenser | Mijia countertop water purifier MRH142b | Normal operation | 1 | 1 |
| Hair dryer | Cetis HD-200; Feike FH6251 | Cool/hot air × low/high settings, including plug insertion and removal | 17 | 17 |
| Mobile phone | iPhone 17 (electromagnetic CT) / HUAWEI mate60pro (Hall CT) | Battery levels: 10/30/50/70/90/100% | 6 | 6 |
| Printer | HP Smart Tank 678 | Standby, printing | 2 | 2 |
| Electric fan | AUX FS1603RC; Shenhua FD40-A4 | Low/medium/high speed | 6 | 6 |
| Air purifier | Haier KJ500F-A01U1 | Sleep mode, auto mode, fan speeds 1–4 | 6 | 6 |
| Laptop | Lenovo Air15 | Battery levels: 5/10/30/50/70/90/100% | 7 | 7 |
| **Total** | **11 models** | | **46** | **46** |

Except for the mobile phones, the two batches for each of the other seven categories were recorded from the same physical device under the same operating conditions. Recordings can therefore be paired by operating condition. However, the two batches were **not recorded simultaneously** (electromagnetic CT recordings were made from 2026-10-27 to 2026-10-30, while Hall CT recordings were made from 2026-08-08 to 2026-11-03). They are suitable for comparing statistical features, but not for point-by-point alignment. The two mobile-phone batches use different models and can only be compared at the level of the same load category.

### Acquisition Equipment Specifications

| Parameter | Specification                                                         |
|---|-----------------------------------------------------------------------|
| Voltage acquisition (AC_V) | Range: 0–300 V; error ≤ 0.5%                                          |
| Current acquisition (AC_I) | Range: 0–150 A (Hall CT), 0 ~ 60 A (electromagnetic CT); error ≤ 0.5% |
| Sampling rate | 12,800 Hz                                                             |
| Bandwidth | DC–1600 Hz                                                            |

## Acquisition Conditions

- Single-phase 220 V / 50 Hz mains supply, recorded using the same waveform recording device with two analog channels: AC_V and AC_I.
- Sampling rate: 12,800 Hz. Samples are stored as 16-bit signed integers (−32768 to 32767).
- The voltage channel records the supply voltage of the appliance under test; the current channel records only the current drawn by that appliance.
- The `readme.txt` file in each appliance directory contains the test notes for that appliance.

## Directory Structure

```text
.
├── data/
│   ├── 电磁CT/<appliance category>/<model>/[<operating condition>/]<recording name>.cfg|.dat
│   └── 霍尔CT/<appliance category>/<model>/[<operating condition>/]<recording name>.cfg|.dat
├── doc/img/
├── LICENSE
├── README.md
├── README_en.md
```

- `<recording name>` follows the pattern `BAYxx_xxxx_YYYYMMDD_HHMMSS_mmm` and matches the start time specified in the CFG file.
- `P` in an operating-condition directory name indicates a percentage. For example, `电量P10` means a battery level of 10%.
- Paths contain Chinese characters. Clone and extract the repository in a UTF-8-compatible environment.

## File Format

Each recording consists of a `.cfg` file and a `.dat` file with the same name, following COMTRADE 1999 (IEEE C37.111-1999).

**CFG** (11 lines, CRLF):

| Line | Content | Value used in this dataset |
|---|---|---|
| 1 | Station name, device name, version | `Demo Station,Demo Device V1.00,1999` |
| 2 | Number of channels | `2,2A,0D` |
| 3–4 | Analog channel definitions | Index, name, phase, circuit component, unit, `a`, `b`, skew, min, max, primary, secondary, PS |
| 5 | Power-system frequency | `50` |
| 6 | Number of sampling-rate sections | `1` |
| 7 | Sampling rate, last sample number | `12800,N` |
| 8 | Start time | `dd/mm/yyyy,hh:mm:ss.ssssss` |
| 9 | Trigger time | Same as the start time |
| 10 | Data format | `ASCII` |
| 11 | Time multiplier | `1.000` |

**DAT** (4 columns per line, CRLF):

```text
Sample number, relative timestamp (µs), AC_V, AC_I
1,0,3267,35
2,78,3232,36
```

Physical quantities are converted according to the corresponding channel row in the CFG file: `actual value = a × sample value + b`. `a` is the 6th field in the channel row, and `b` is the 7th field. Since the ranges differ between appliances, always read the conversion coefficients from the corresponding CFG file; do not apply a single set of coefficients to all recordings.

### Timestamp Notes

The second column of the DAT file gives the time in microseconds **relative to the start time**. It forms a linear time axis and satisfies `t_i = (i−1) × 10⁶ / 12800`. The interval alternates between 78 and 79 µs, is monotonically increasing, and the final timestamp differs by less than 1 µs from `start time + (N−1)/sampling rate` in the CFG file.

## Quick Start

Environment: Python 3.8 or later. The example below requires NumPy.

```python
import numpy as np

def load(cfg_path):
    """Load one recording and return t (s), u (V), and i (A)."""
    lines = open(cfg_path, encoding='ascii').read().splitlines()
    nch = int(lines[1].split(',')[1].rstrip('A'))
    ch = [x.split(',') for x in lines[2:2 + nch]]
    dat = np.loadtxt(cfg_path[:-4] + '.dat', delimiter=',')

    t = dat[:, 1] / 1e6
    u = dat[:, 2] * float(ch[0][5]) + float(ch[0][6])
    i = dat[:, 3] * float(ch[1][5]) + float(ch[1][6])
    return t, u, i

t, u, i = load('data/电磁CT/电风扇/奥克斯FS1603RC/高档风速/BAYxx_xxxx_20261029_093457_017.cfg')
print(t.shape, u.shape, i.shape)
```

## Known Limitations

- **Fixed windows, no event labels:** The trigger time is identical to the start time (as in the source data). Recordings are fixed-length windows and do not include pre-trigger segments. The timing of events such as startup, shutdown, and plug insertion/removal must be identified from the waveforms.
- **Batch differences at low current levels:** Under low-power conditions such as standby and low-speed fan operation, the two batches show noticeable differences in current RMS values. Absolute values should not be directly equated across batches. Comparisons should preferably use relative changes within the same batch or waveform features.
- **Differences between models and individual units:** The two mobile-phone batches use an iPhone 17 and a HUAWEI mate60pro, respectively. The recordings represent measurements from specific physical units; individual devices of the same model may differ.

## License

- **Data** (`data/`): CC BY 4.0. See [LICENSE](LICENSE).
- When redistributing the dataset or publishing work based on it, retain the attribution and license notices and specify the dataset version.

## Citation

If this dataset is useful in your work, please cite the data source as follows:

> PA-WAVE/Nanjing PowerAnt/Residential Appliance Waveform Dataset v1.0/2026-11

## Feedback

- If you find data errors or format issues, or have suggestions for use, please submit an Issue.
