Quality-Aware Decoding
===

A repository for experiments in quality-aware decoding.

## Setup 

Start by installing the correct version of pytorch for your system.
The rest of the requirements can be installed by running
```bash
pip install -r requirements.txt
```

Experimentation is based on [ducttape](https://github.com/jhclark/ducttape).
Start by installing it. We recommend installing [version 0.5](https://github.com/CoderPat/ducttape/releases/tag/v0.5)

Finally, for experiments involving reranking, [travatar](https://github.com/neubig/travatar). 
Refer to official documentation on how to compile it. 
After installing set the enviroment variable to location of the compiled project
```bash
export TRAVATAR_DIR=/path/of/compiled/travatar/
```

### BLEURT

Evaluating using BLEURT requires tensorflow, making it incompatible with the requirements for the current env.
Therefor the approach took is to use a separate virtual environment for BLEURT. You can do this by
```bash
python -m venv $BLEURT_ENV
source $BLEURT_ENV/bin/activate
pip install git+git://github.com/google-research/bleurt.git@master
pip install --force-reinstall tensorflow-gpu
```

## Running Experiments


The experiments are organized into two files 

* `tapes/main.tape`: This contains the task definitions. It's where you should add new tasks and functionally or edit previously defined ones.
* `tapes/EXPERIMENT_NAME.tconf`: This is where you define the variables for experiments, as well as which tasks to run.

To start off, we recommend creating you own copy of `tapes/iwsl14.tconf`. 
This file is organized into two parts: (1) the variable definitions at the `global` block (2) the plan definition

To start off, you need to edit the variables to correspond to paths in your file systems. 
Examples include the `$repo` variable and the data variables.

Then try running one of the existing plans by executing

```bash
ducttape tapes/main.tape -C $my_tconf -p Baseline -j $num_jobs
```

`$num_jobs` corresponds to the number of jobs to run in parallel. `num_jobs=8` will run 8 branches in parallel if possible (correspondely using 8 GPUs)
To get a summary of the different paths run

```bash
ducttape tapes/main.tape -C tapes/flores101.tconf -p summary > test_summary.tsv

```
When you run the Baseline plan, you don't get scores for the dev scores. To get the scores, you need to run another plan:
```bash
ducttape tapes/main.tape -C $my_tconf -p ScoreDevBaseline -j $num_jobs
```

After that, you can run a summary mode to get the summary of the dev scores. Make sure you run it immediately after ScoreDev plan.
```bash
ducttape tapes/main.tape -C tapes/flores101.tconf -p ScoreDevBaseline summary > dev_summary.tsv
```