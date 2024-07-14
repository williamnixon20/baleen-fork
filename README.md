# Artifact for Baleen (FAST 2024)

_Baleen: ML Admission & Prefetching for Flash Caches_

_[Paper (Preprint)](https://wonglkd.fi-de.net/papers/Baleen-FAST24.pdf) | [Code](https://github.com/wonglkd/BCacheSim/) | [Data](https://ftp.pdl.cmu.edu/pub/datasets/Baleen24/) | [Video walkthrough](https://www.tiny.cc/BaleenArtifactYT) | [Reproduce on Chameleon](https://www.chameleoncloud.org/experiment/share/aa6fb454-6452-4fc8-994a-b028bfc3c82d)_ 

This repository is targeted at those seeking to reproduce the results found in the Baleen paper and contains a frozen copy of the code.
If you are looking to use Baleen, please go to https://github.com/wonglkd/BCacheSim/ for the latest version.

![Artifact Available](https://sysartifacts.github.io/images/usenix_available.svg)
![Artifact Functional](https://sysartifacts.github.io/images/usenix_functional.svg)
![Results Reproduced](https://sysartifacts.github.io/images/usenix_reproduced.svg)

**Scope:** this repository contains Python code to reproduce the **simulator** results in the Baleen paper. The testbed code modified a proprietary internal version of CacheLib and will not be released at this time, pending a rebase on the open-source version of CacheLib. Another key difference is that Meta's exact constants for the disk head time function will not be released, meaning that results will not be exactly the same; instead, we use constants (seek time and bandwidth) measured on the hard disks in our university testbed.

**Nomenclature:**
Some terms were renamed after coding for better clarity in the paper. However, they mean the same thing.

- Service Time (in the code) was renamed to Disk Head Time (in the paper)
- Chunks (in the code) are called segments (in the paper)


## Walkthrough Video
We have verified that our instructions work on Chameleon, and have recorded a video showing the setup of the environment and the reproduction of the instructions below (YouTube: http://tiny.cc/BaleenArtifactYT). This video shows the setup on Chameleon, the running of the instructions below and the running of all notebooks successfully run with no error cells.

[![Baleen Artifact Walkthrough (FAST 2024)](https://github.com/wonglkd/Baleen-FAST24/assets/2821951/4b1f348b-35fc-4262-a69f-a754c0ec99b9)](https://www.tiny.cc/BaleenArtifactYT)

## Getting Started

_Time estimate: 60 mins (20 mins interactive)._

### Installation (Chameleon Trovi)

_Time estimate: 30 minutes (10 mins interactive)._

The recommended way is to use Chameleon Trovi, an academic cloud. Note that you will require an allocation; if you are affiliated with FAST, you can request to be added to the associated project (CHI-231080). To do this (and for any other issues with Chameleon), please contact the helpdesk at help@chameleoncloud.org.

1. Launch [artifact on Trovi](https://www.chameleoncloud.org/experiment/share/aa6fb454-6452-4fc8-994a-b028bfc3c82d)
2. (Optional) Open notebook `chameleon/1-getting-started.ipynb` which will walk you through the Getting Started section of this README. You may run one cell at a time, or click Run -> Run All Cells to execute all commands. If processes get killed, you need a dedicated server.
3. (Recommended) The shared JupyterHub has limited RAM/disk. Run notebook `chameleon/2-start-dedicated-server.ipynb`, which provisions a beefier node (for 7 days) that you can create a SSH tunnel to.

### Installation (local computer)

Alternatively, you may do a manual install. These commands are also available in [getting-started.sh](getting-started.sh) for your convenience.

1. Clone the repository (if not already done)

```
git clone --recurse-submodules https://github.com/wonglkd/Baleen-FAST24.git
cd Baleen-FAST24
```

Note: this repository uses submodules. As a reminder, when you pull, you'll likely want to use `git pull --recurse-submodules`.

2. Install Python dependencies with Conda/Mamba/Micromamba or pip. (We developed with Micromamba 1.4.1.)

```
conda env create -f BCacheSim/install/env_cachelib-py-3.11.yaml
conda activate cachelib-py-3.11
# PyPy is optional (for faster non-ML runs)
# conda env create -f BCacheSim/install/env_cachelib-pypy-3.8.yaml
```

Alternatively, use pip:

```
python3 -m pip install --user -r BCacheSim/install/requirements.txt
```

3. Download trace files (see [here](https://ftp.pdl.cmu.edu/pub/datasets/Baleen24/) for more details on the traces)

```
cd data
bash get-tectonic.sh
```

### Do a simple experiment

_Time estimate: 30 minutes (10 mins interactive)._

1. Manually run the simulator with the baseline RejectX. (4 mins)

```
./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --config runs/example/rejectx/config.json
```
OPT PER DAY
./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir ./tmp/optday0 --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-split-secs-start 0 --train-split-secs-end 86400 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk

./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir ./tmp/optday1 --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-split-secs-start 86400 --train-split-secs-end 172800 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk

./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir ./tmp/optday2 --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-split-secs-start 172800 --train-split-secs-end 259200 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk


./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir ./tmp/optday3 --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-split-secs-start  259200  --train-split-secs-end 345600 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk


./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir ./tmp/optday4 --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-split-secs-start  345600   --train-split-secs-end 432000 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk


./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir ./tmp/optday5 --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-split-secs-start  432000   --train-split-secs-end 518400 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk

./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir ./tmp/optday6 --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-split-secs-start 518400    --train-split-secs-end 604800
--ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk

2. Manually train Baleen's ML models (25 secs) and run the simulator with Baleen (~30 mins).

```
./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir runs/example/baleen --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-models admit prefetch --train-split-secs-start 0 --train-split-secs-end 86400 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk

./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy P
olicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir runs/example/baleen --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-models admit prefetch --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk


./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --config runs/example/baleen/prefetch_opt/config.json
```

## OPT

./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp exp_reproduce_ --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0.0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.47461 --output-base-dir ./tmp/exp_reproduce_20230403_19traces --eviction-age 4752.887 --train-target-wr 35.599 --rl-init-kwargs filter_=prefetch --train-models admit prefetch 	

# RUN OPT PER DAY FULL
./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --trace data/tectonic/201910/Region1/full_0_0.1.trace --offline-ap --ap opt --ap-threshold 5 --size_gb 366.475 -o ./runs/ap_opt_per_day/threshold/5 --prefetch-when at_start --prefetch-range acctime-episode --batch-size 16 --log-interval 600  --ep-analysis tmp/exp_reproduce_20230403_19traces/exp_reproduce_/201910_Region1_0_0.1/offline_analysis_ea_4752.89.csv --offline-ap-decisions decisions_opt_per_day.pkl.bz --eviction-policy LRU

## OPT REGULER FULL POLL
./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir ./tmp/optfull --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk

./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --trace data/tectonic/201910/Region1/full_0_0.1.trace --offline-ap --ap opt --ap-threshold 15 --size_gb 366.475 -o ./runs/ap_opt_full/threshold/15 --prefetch-when at_start --prefetch-range acctime-episode --batch-size 16 --log-interval 600  --ep-analysis tmp/exp_reproduce_20230403_19traces/exp_reproduce_/201910_Region1_0_0.1/offline_analysis_ea_4752.89.csv --offline-ap-decisions /home/cc/Baleen-FAST24/tmp/optfull/example/201910_Region1_0_0.1/decisions_utility_service_time_size_fixed_ea_5892.86.pkl.bz --eviction-policy LRU

3. Use [notebooks/example/example.ipynb](notebooks/example/example.ipynb) to view and plot results.


## REGION 1

# CoinFlip, Region1, 0.0
./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp exp_reproduce_ --policy PolicyUtilityHits --region Region1 --sample-ratio 0.1 --sample-start 0.0 --trace-group 201910  --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.47461 --output-base-dir ./tmp/exp_reproduce_20230412_19traces_1p  --eviction-age 7182.692 --train-target-wr 35.599 --rl-init-kwargs filter_=noprefetch --train-models admit prefetch --train-split-secs-start 0 --train-split-secs-end 86400 

## COINFLIP
./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --trace data/tectonic/201910/Region1/full_0_0.1.trace --coinflip-ap --ap coinflip --ap-probability 0.077218 --size_gb 366.475 -o ../runs/exp_reproduce_spring23/20230412_19traces_1p/201910_Region1_0_1_366.475GB_WR35.599MBS/ap_coinflip/policy_hits/prefetch_never_episode/i_2_ea_7182.69   --prefetch-when never --prefetch-range episode  --log-interval 600  --ep-analysis ../runs/exp_reproduce_spring23/20230412_19traces_1p/201910_Region1_0_1/offline_analysis_ea_7182.69.csv --offline-ap-decisions ../tmp/20230412_19traces_1p/201910_Region1_0_1/decisions_utility_hits_fixed_ea_7182.69.pkl.bz --eviction-policy LRU
# RejectX, Region1, 0.0
./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp exp_reproduce_ --policy PolicyUtilityHits --region Region1 --sample-ratio 0.1 --sample-start 0.0 --trace-group 201910  --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.47461 --output-base-dir ./tmp/exp_reproduce_20230412_19traces_1p  --eviction-age 6529.048 --train-target-wr 35.599 --rl-init-kwargs filter_=noprefetch --train-models admit prefetch --train-split-secs-start 0 --train-split-secs-end 86400 

# rejectx run
./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --trace data/tectonic/201910/Region1/full_0_0.1.trace --rejectx-ap --ap rejectx --ap-probability 0.580327 --size_gb 366.475 -o ../runs/exp_reproduce_spring23/20230412_19traces_1p/201910_Region1_0_1_366.475GB_WR35.599MBS/ap_rejectx/policy_hits/prefetch_never_episode/i_2_ea_6529.05   --prefetch-when never --prefetch-range episode  --log-interval 600  --ep-analysis ../runs/exp_reproduce_spring23/20230412_19traces_1p/201910_Region1_0_1/offline_analysis_ea_6529.05.csv --ap-threshold 1 --offline-ap-decisions ../tmp/20230412_19traces_1p/201910_Region1_0_1/decisions_utility_hits_fixed_ea_6529.05.pkl.bz --eviction-policy LRU

# Baleen (No Prefetch), Region1, 0.0
./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp exp_reproduce_ --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0.0 --trace-group 201910  --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.47461 --output-base-dir ../tmp/exp_reproduce_20230412_19traces_1p  --suffix /201910_Region1_0_1/fs_meta+block+chunk/accs_15   --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk  --eviction-age 6119.971 --train-target-wr 35.599 --rl-init-kwargs filter_=noprefetch --train-models admit prefetch --train-split-secs-start 0 --train-split-secs-end 86400 

## RUN LEARNED 
./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --trace data/tectonic/201910/Region1/full_0_0.1.trace --learned-ap --ap mlnew --ap-threshold 0.686769 --size_gb 366.475 -o ../runs/exp_reproduce_spring23/20230412_19traces_1p/201910_Region1_0_1_366.475GB_WR35.599MBS/ap_mlnew/policy_servicetimesize/prefetch_never_episode/i_2_ea_6119.97   --prefetch-when never --prefetch-range episode  --batch-size 16  --log-interval 600  --ep-analysis ../runs/exp_reproduce_spring23/20230412_19traces_1p/201910_Region1_0_1/fs_meta+block+chunk/accs_15/offline_analysis_ea_6119.97.csv --learn-ap-filtercount 6 --learn-ap-granularity both --offline-ap-decisions ./tmp/20230412_19traces_1p/201910_Region1_0_1/fs_meta+block+chunk/accs_15/decisions_utility_service_time_size_fixed_ea_6119.97.pkl.bz --learned-ap-model-path ./tmp/exp_reproduce_/201910_Region1_0_0.1/201910_Region1_0_1/fs_meta+block+chunk/accs_15/ea_6119.97_wr_35.599_admit_threshold_binary.model --ap-feat-subset meta+block+chunk --eviction-policy LRU

# Baleen, Region1, 0.0
./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp exp_reproduce_ --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0.0 --trace-group 201910  --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.47461 --output-base-dir ../tmp/exp_reproduce_20230412_19traces_1p  --suffix /201910_Region1_0_1/fs_meta+block+chunk/accs_15   --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk  --eviction-age 6592.988 --train-target-wr 35.599 --rl-init-kwargs filter_=prefetch --train-models admit prefetch --train-split-secs-start 0 --train-split-secs-end 86400 

# RUN BALEEN
./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --trace data/tectonic/201910/Region1/full_0_0.1.trace --learned-ap --ap mlnew --ap-threshold 0.830268 --size_gb 366.475 -o ../runs/exp_reproduce_spring23/20230412_19traces_1p/201910_Region1_0_1_366.475GB_WR35.599MBS/ap_mlnew/policy_servicetimesize/prefetch_predict_acctime-episode-predict/i_2_ea_6592.99   --prefetch-when predict --prefetch-range acctime-episode-predict  --batch-size 16  --prefetch-when-threshold 0.5  --log-interval 600  --ep-analysis ../runs/exp_reproduce_spring23/20230412_19traces_1p/201910_Region1_0_1/fs_meta+block+chunk/accs_15/offline_analysis_ea_6592.99.csv --learn-ap-filtercount 6 --learn-ap-granularity both --prefetcher-model-path ./tmp/exp_reproduce_/201910_Region1_0_0.1/201910_Region1_0_1/fs_meta+block+chunk/accs_15/ea_6592.99_wr_35.599_prefetch_{k}.model --offline-ap-decisions ../tmp/20230412_19traces_1p/201910_Region1_0_1/fs_meta+block+chunk/accs_15/decisions_utility_service_time_size_fixed_ea_6592.99.pkl.bz --learned-ap-model-path ./tmp/exp_reproduce_/201910_Region1_0_0.1/201910_Region1_0_1/fs_meta+block+chunk/accs_15/ea_6592.99_wr_35.599_admit_threshold_binary.model --ap-feat-subset meta+block+chunk --eviction-policy LRU

## Detailed Instructions

This section assumes you have completed the 'Getting Started' section and have
installed the code and downloaded the traces.

As it requires too much computation time to rerun every single experiment,
we suggest the following steps to maximize the use of reviewers' time in evaluating
our paper. We supply our traces, code, and the intermediate results from our experimental runs.

**Roadmap for evaluation:**

1. Test out Baleen's ML training & simulator (in Getting Started).
    - What: simulate RejectX baseline, train Baleen models, simulate Baleen
    - Expected results: notebooks/example/example.ipynb
2. Plot graphs using our intermediate results.
    - Example: notebooks/paper-figs/fig-01bc,17-202309.ipynb
3. Select additional simulations to run if desired.
    - See notebooks/reproduce/, in particular commands.ipynb and reproduce_commands.sh


## Directory structure

- data: traces that are used as input
- runs: where experiment results are stored
- tmp: temporary directory for ML models, generated episode files
- notebooks: Jupyter notebooks for experiments
- notebooks/figs: Output directory for figures


## Additional notes

624 machine-days were used for the final runs to generate the results used in the paper.
Each simulation of a ML policy takes at least 30 minutes, multiplied by 7 traces and 10 samples each.

## Future research

notebooks/reproduce/exps-cluster-sample.ipynb will be useful to allow you to run experiments efficiently, but with more dependencies required (brooce, redis).

## Troubleshooting

If you face any issues, please try the following things:

1. Making sure you have the latest version of the repository

```
git pull --recurse-submodules
```

2. Making sure you have the latest copy of the data.

```
cd data
bash clean.sh
bash get-tectonic.sh
```

3. If you need to get an allocation on Chameleon or face any difficulties with the platform itself, please contact their [helpdesk](mailto:help@chameleoncloud.org).

## Any questions?

[Please raise a GitHub issue](https://github.com/wonglkd/Baleen-FAST24/issues/new). Support is best effort; you may also email me (contact details at https://wonglkd.fi-de.net).

## Reference

**[Baleen: ML Admission & Prefetching for Flash Caches](https://www.usenix.org/conference/fast24/presentation/wong)**<br>
Daniel Lin-Kit Wong, Hao Wu, Carson Molder, Sathya Gunasekar, Jimmy Lu, Snehal Khandkar, Abhinav Sharma, Daniel S. Berger, Nathan Beckmann, Gregory R. Ganger<br>
USENIX FAST 2024
