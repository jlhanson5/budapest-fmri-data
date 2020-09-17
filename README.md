

[![DOI](https://zenodo.org/badge/251371344.svg)](https://zenodo.org/badge/latestdoi/251371344)


# An fMRI dataset in response to "The Grand Budapest Hotel", a socially-rich, naturalistic movie

This repository contains code related to the fMRI dataset collected while participants watched [The Grand BudapestHotel](https://en.wikipedia.org/wiki/The_Grand_Budapest_Hotel) by Wes Anderson. The associated manuscript *An fMRI dataset in response to "The Grand Budapest Hotel", a socially-rich, naturalistic movie* by Matteo Visconti di Oleggio Castello, Vassiki Chauhan, Guo Jiahui, & M. Ida Gobbini is available as a preprint [here](https://www.biorxiv.org/content/10.1101/2020.07.14.203257v1).

If you use the dataset, please cite the corresponding preprint:

Visconti di Oleggio Castello, M., Chauhan, V., Jiahui, G., & Gobbini, M. I. (2020). *An fMRI dataset in response to "The Grand Budapest Hotel", a socially-rich, naturalistic movie*. In bioRxiv (p. 2020.07.14.203257). https://doi.org/10.1101/2020.07.14.203257

The dataset is available on OpenNeuro: https://openneuro.org/datasets/ds003017. See below for information on how to install the dataset.

This repository can be cited as follows:

Visconti di Oleggio Castello, M., Chauhan,  V., Jiahui, G., & Gobbini, M. I. (2020).  *mvdoc/budapest-fmri-data: v0.0.1 (Version v0.0.1)*. Zenodo.  http://doi.org/10.5281/zenodo.3942174

## Dataset download

The dataset can be downloaded from [OpenNeuro, dataset ds003017]( https://openneuro.org/datasets/ds003017). Alternatively, it can be downloaded using [DataLad](https://www.datalad.org/). Once DataLad is installed in your system, the dataset can be installed as follows

```bash
$ datalad install ///labs/gobbini/budapest/openneuro
```

Please refer to the [DataLad handbook](http://handbook.datalad.org/en/latest/) to learn how to use DataLad.

## Setting up a python environment

We provide a conda environment file to set up an appropriate python environment for the preprocessing scripts. This environment has been tested on Linux and Mac OS X, however there's a chance it might not work on your system. Please feel free to open an issue here and we'll try to help.

Assuming you have already installed [anaconda or miniconda](https://docs.anaconda.com/anaconda/install/) on your system, you can set up a new conda environment with requirements as follows (note that it can take a while):

```bash
$ conda env create -f conda-environment.yml --name budapest
```

Once all packages have been installed, you should activate the environment and install an additional python package that we provide which contains additional helper functions:

```bash
$ conda activate budapest
$ pip install ./code
```

## Presentation, preprocessing, and quality assurance scripts

In this repository we provide the scripts used to generate and preprocess the stimuli, to present the stimuli in the scanner, to preprocess the fMRI data, and to run quality assurance analyses. These scripts can be found in the [`scripts`](scripts) directory. In particular,

- [scripts/preprocessing-stimulus](scripts/preprocessing-stimulus) contains the scripts to
  split the movie into separate parts to be presented in the scanner, and preprocess the audio of the movie to make it more audible in the scanner.
- [scripts/presentation](scripts/presentation) contains PsychoPy presentation scripts.
- [scripts/preprocessing-fmri](scripts/preprocessing-fmri) contains the scripts used to run [fMRIprep](https://fmriprep.readthedocs.io/) for preprocessing.
- [scripts/quality-assurance](scripts/quality-assurance) contains scripts to run QA analyses and generate the figures reported in the data paper.
- [scripts/hyperalignment-and-decoding](hyperalignment-and-decoding) contains scripts to perform hyperalignment and movie segment classification.

Below we describe the content of these directories in more detail.

### Stimulus preprocessing

The movie was extracted from a DVD and converted into mkv (`libmkv 0.6.5.1`) format using [HandBrake](https://handbrake.fr/). Unfortunately, this process was not scripted. The DVD had [UPC code 024543897385](https://www.upcitemdb.com/upc/24543897385). We provide additional metadata associated with the converted movie file to make sure that future conversions would match our stimuli as best as possible. The information is available in [`scripts/preprocessing-stimulus/movie-file-info.txt`](scripts/preprocessing-stimulus/movie-file-info.txt). In particular, the total video duration was `01:39:55.17`. The video and audio were encoded with the following codecs:

```
Stream #0:0(eng): Video: h264 (High), yuv420p(tv, smpte170m/smpte170m/bt709, progressive), 720x480 [SAR 32:27 DAR 16:9], SAR 186:157 DAR 279:157, 30 fps, 30 tbr, 1k tbn, 60 tbc (default)
Stream #0:1(eng): Audio: ac3, 48000 Hz, stereo, fltp, 160 kb/s (default)
Stream #0:2(eng): Audio: ac3, 48000 Hz, 5.1(side), fltp, 384 kb/s
```

Once the movie was extracted and converted, it was split into different parts for a behavioral session and five imaging runs. The times for the behavioral session are available in [`scripts/preprocessing-stimulus/splits_behav.txt`](scripts/preprocessing-stimulus/splits_behav.txt). These first ~45 minutes of the movie were shown outside the scanner, right before the imaging session. The times of the five additional splits of the second part of the movie are available in [`scripts/preprocessing-stimulus/splits.txt`](scripts/preprocessing-stimulus/splits.txt). Each row indicates a pair of start/end times for each subclip.

We also provide the scripts used to generate these splits, which used `ffmpeg`. While the movies were converted, the audio was also postprocessed and passed through an audio compressor to reduce the dynamic range and make dialogues more audible in the scanner. These scripts are  [`scripts/preprocessing-stimulus/split_movie_behav.sh`](scripts/preprocessing-stimulus/split_movie_behav.sh) and [`scripts/preprocessing-stimulus/split_movie.sh`](scripts/preprocessing-stimulus/split_movie.sh) for the behavioral and imaging sessions respectively. They will produce six files named `budapest_part[1-6].mp4` that were used for the experiment.

During the first anatomical scan, subjects were shown the last five minutes of `budapest_part1.mp4` so that they could select an appropriate volume for the remaining five scans. The clip showed during the anatomical scan is generated by the script [`scripts/preprocessing-stimulus/split_part1_soundcheck.sh`](scripts/preprocessing-stimulus/split_part1_soundcheck). This script will generate a file named `budapest_soundcheck.mp4`. 

### Presentation scripts

For the behavioral session outside the scanner, subjects were  shown `budapest_part1.mp4` (generated as described above) using VLC and high-quality headphones. They were free to adjust the volume as much as they liked, and no instructions were given.

All presentation scripts used [PsychoPy](https://www.psychopy.org/). Unfortunately, we are unable to acess the computer used for presentation, so we cannot provide the specific version used in our experiment. Any recent version of PsychoPy should be able to run the presentation code. Feel free to open an issue on this repository if you encounter problems.

All presentation scripts assume that the stimuli are placed in a subdirectory named `stim`.

During the anatomical scan, subjects were shown the last five minutes of the part they just saw to select an appropriate volume level. The presentation script used for this run is [`scripts/presentation/soundcheck.py`](scripts/presentation/soundcheck.py). The subject can decrease/increase the volume using the buttons `1` and `2` respectively. Once the script has run, it will save the volume information in a json file called `subjectvolume.json`. An example of this file will look like as follows

```json
{
 "sid000020": 1.0,
 "sid000021": 0.5,
 "sid000009": 0.75,
}
```

The presentation script used for the functional imaging runs is [`scripts/presentation/show_movie.py`](scripts/presentation/show_movie.py). Some (limited) config values can be defined in the config json file [`scripts/presentation/config.json`](scripts/presentation/config.json). Once the presentation script is run, it will show a dialog box to select the subject id and the run number. The volume will be automatically selected by loading the volume information stored in `subjectvolume.json`. Log files will be stored in a subdirectory named `res`. It is possible to stop the experiment at any point using `CTRL + q`. In that case, the logs will be flushed, saved, and moved to a file with suffix `__halted.txt`. 

The logs save detailed timing information (perhaps eccessive) about each frame. By default, useful information for extracting event files is logged with a `BIDS` log level. Thus, one can easily generate a detailed events file by simply grepping `BIDS`. For example

```bash
$ grep BIDS sub-test_task-movie_run-1_20200916T114100.txt | awk '{for (i=3; i<NF; i++) printf $i"\t";print $NF}' | head -20
onset	duration	frameidx	videotime	lasttrigger
10.008	{duration:.3f}	1	0.000	9.000
10.009	{duration:.3f}	2	0.000	10.008
10.011	{duration:.3f}	3	0.000	10.008
10.013	{duration:.3f}	4	0.000	10.008
10.015	{duration:.3f}	5	0.000	10.008
10.019	{duration:.3f}	6	0.000	10.008
10.021	{duration:.3f}	7	0.000	10.008
10.032	{duration:.3f}	8	0.000	10.008
10.045	{duration:.3f}	9	0.000	10.008
10.059	{duration:.3f}	10	0.033	10.008
10.072	{duration:.3f}	11	0.033	10.008
10.085	{duration:.3f}	12	0.033	10.008
10.099	{duration:.3f}	13	0.067	10.008
10.112	{duration:.3f}	14	0.067	10.008
10.125	{duration:.3f}	15	0.100	10.008
10.139	{duration:.3f}	16	0.100	10.008
10.152	{duration:.3f}	17	0.100	10.008
10.165	{duration:.3f}	18	0.133	10.008
10.179	{duration:.3f}	19	0.133	10.008
```

The available columns are `onset` (frame onset); `duration` (containing a python format string so that duration information can be added with a trivial parser); `frameidx` (index of the frame shown); `videotime` (time of the video); `lasttrigger` (time of the last received trigger).

### fMRI preprocessing with fMRIprep

The dataset was preprocessed using [fMRIprep](https://fmriprep.org) (version 20.1.1) in a singularity container. To obtain the container, simply run the following line (assuming you have singularity installed):

```bash
VERSION="20.1.1"; singularity build fmriprep-"$VERSION".simg docker://poldracklab/fmriprep:"$VERSION"
```

We provide the scripts that were used to run fMRIprep on the Dartmouth HPC cluster ([Discovery](https://rc.dartmouth.edu/index.php/discovery-overview/)). Please use those as an example and refer to the documentation of fMRIprep for more details on preprocessing.

### Quality assurance scripts

### Hyperalignment and decoding scripts

## Notebooks