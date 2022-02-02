# Do You Have the Energy for This Meeting? An Empirical Study on the Energy Consumption of Android Videoconferencing Apps
This repository is a companion page of the paper _"Do You Have the Energy for This Meeting? An Empirical Study on the Energy Consumption of Android Videoconferencing Apps"_, submitted for review at the 9th IEEE/ACM International Conference on Mobile Software Engineering and Systems (MOBILESoft), 2022.

The study aims to investigate the energy consumption of two popular Android videoconferencing apps, namely Zoom and Google Meet. 
In addition we investigate the impact on energy consumption of different features and settings, namely number of participants, and camera, michropone, and virtual background use.

This repository is devided into two main parts: the actual experiment (`android-runner`) and the analysis of the measured data (`data-analysis`).

## android-runner
Python framework for automatically executing measurement-based experiments on native and web apps running on Android devices

## Data analysis
The data analysis can again be split up into two parts: the preprocessing and the actual analysis.

### Preprocessing
In order to be able to evaluate the measured data, it is reasonable to extract the relevant information that Batterystats has written into multiple files spread over multiple folders. We used a Python script to do this. Before running, a few parameters have to be adapted in this script:
- `phone`: The ID of the used phone as specified in Monkeyrunner (e.g. `motog5`)
- `raw_data_path`: The path to the folder containing the raw data generated by the experiment.

There are two preprocessing scripts available: one for the preliminary experiment (`preprocessing-pre.py`) and one for the main experiment (`preprocessing-main.py`). They slightly differ, since the folder structure and the interesting attributes also differ. Both are generating a CSV file called `results-pre.csv` or `results-main.csv` respectively in the raw data’s parent folder. This CSV file contains the following attributes:
| Attribute | Description | Values |
| ----------- | ----------- | ----|
| `app` | The examined videoconferencing app |`meet` or `zoom` |
| `number_of_participants` (only in main)|Number of participants in the video conference|`2` or `5`|
| `camera` (only in main)|Activation state of the camera|`True` or `False`|
| `microphone` (only in main)|Activation state of the microphone|`True` or `False`|
| `background` (only in main)|Activation state of the virtual background|`True` or `False`|
| `duration_in_minutes` (only in preliminary)| Duration of the video conference in minutes|Numerical value|
| `joules`|Consumed energy in Joules|Numerical value|

#### Folder structure
Inside the specified `raw_data_path` folder must be the following folder structure for the preprocessing script to find the measurements and used treatments.
##### Preliminary experiment:
- Folders with arbitrary names, all containing:
    - Config file: `config.json`
    - Folder: `data`
        - Folder: Name of the phone (as specified in the variable `phone`)
            - Folder: Either `com-google-android-apps-meetings` or `us-zoom-videomeetings`
                - Folder: `batterystats`
                    - CSV files starting with `Joule` containing the energy measurements

##### Main experiment:
- Folders with arbitrary names ending with either `-2p` (for two participants) or `-5p` (for five participants), all containing:
    - Folders with arbitrary names ending with a hypen (`-`) followed by a subset of the letters `c` (iff the camera was active), `b` (iff the virtual background was active) and `m` (iff the microphone was active) – for example: `somefoldername-cbm`
        - Config file: `config.json`
        - Folder: `data`
            - Folder: Name of the phone (as specified in the variable `phone`)
                - Folder: Either `com-google-android-apps-meetings` or `us-zoom-videomeetings`
                    - Folder: `batterystats`
                        - CSV files starting with `Joule` containing the energy measurements

### Acutal analysis
To analyse and visualize the preprocessed data, we used R. With R it is very simple to read CSV files and perform analyses on the read data. For creating plots, we used ggplot2.
