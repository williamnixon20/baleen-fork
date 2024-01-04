# Artifact for Baleen (FAST 2024)

_Paper: [Baleen: ML Admission & Prefetching for Flash Caches](https://wonglkd.fi-de.net/papers/Baleen-FAST24.pdf)_

This repository is targeted at those seeking to reproduce the results found in the Baleen paper and contains a frozen copy of the code.
If you are looking to use Baleen, please go to https://github.com/wonglkd/BCacheSim/ for the latest version.

**Scope:** this repository contains Python code to reproduce the **simulator** results in the Baleen paper. The testbed code modified a proprietary internal version of CacheLib and will not be released at this time, pending a rebase on the open-source version of CacheLib. Another key difference is that Meta's exact constants for the disk head time function will not be released, meaning that results will not be exactly the same; instead, we use constants (seek time and bandwidth) measured on the hard disks in our university testbed.

**Nomenclature:**
Some terms were renamed after coding for better clarity in the paper. However, they mean the same thing.

- Service Time (in the code) was renamed to Disk Head Time (in the paper)
- Chunks (in the code) are called segments (in the paper)

## Getting Started

_Time estimate: 60 mins (20 mins interactive)._

### Installation (Chameleon Trovi)

_Time estimate: 30 minutes (10 mins interactive)._

The recommended way is to use Chameleon Trovi, an academic cloud. 

1. Launch [artifact on Trovi](https://www.chameleoncloud.org/experiment/share/aa6fb454-6452-4fc8-994a-b028bfc3c82d)
2. Open notebook `chameleon/1-getting-started.ipynb` which will walk you through the Getting Started section of this README. You may run one cell at a time, or click Run -> Run All Cells to execute all commands. If processes get killed, you need a dedicated server.
3. (Recommended) The shared JupyterHub has limited RAM/disk. Run notebook `chameleon/2-start-dedicated-server.ipynb`, which provisions a beefier node (for 7 days) that you can create a SSH tunnel to.

### Installation (local computer)

Alternatively, you may do a manual install. These commands are also available in [getting-started.sh](getting-started.sh) for your convenience.

1. Clone the repository (if not already done)

```
git clone --recurse-submodules https://github.com/wonglkd/Baleen-FAST24.git
cd Baleen-FAST24
```

Note: this repository uses submodules. As a reminder, when you pull, you'll likely want to use `git pull --recurse-submodules`.

2. Install Python dependencies with Conda (recommended) or pip.

```
conda env create -f BCacheSim/install/env_cachelib-py-3.11.yaml
conda activate --name cachelib-py-3.11
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

2. Manually train Baleen's ML models (25 secs) and run the simulator with Baleen (~30 mins).

```
./BCacheSim/run_py.sh py -B -m BCacheSim.episodic_analysis.train --exp example --policy PolicyUtilityServiceTimeSize2 --region Region1 --sample-ratio 0.1 --sample-start 0 --trace-group 201910 --supplied-ea physical --target-wrs 34 50 100 75 20 10 60 90 30 --target-csizes 366.475 --output-base-dir runs/example/baleen --eviction-age 5892.856 --rl-init-kwargs filter_=prefetch --train-target-wr 35.599 --train-models admit prefetch --train-split-secs-start 0 --train-split-secs-end 86400 --ap-acc-cutoff 15 --ap-feat-subset meta+block+chunk
./BCacheSim/run_py.sh py -B -m BCacheSim.cachesim.simulate_ap --config runs/example/baleen/prefetch_ml-on-partial-hit/config.json
```

3. Use [notebooks/example/example.ipynb](notebooks/example/example.ipynb) to view and plot results.


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
3. Select additional simulations to run if desired.


## Directory structure

- data: traces that are used as input
- runs: where experiment results are stored
- tmp: temporary directory for ML models, generated episode files
- notebooks: Jupyter notebooks for experiemnts
- notebooks/figs: Output directory for figures


## Additional notes

624 machine-days were used for the final runs to generate the results used in the paper.
Each simulation of a ML policy takes at least 30 minutes, multiplied by 7 traces and 10 samples each.


## Any questions?

Feel free to contact me: https://wonglkd.fi-de.net
