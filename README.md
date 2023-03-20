# Tutorial for Gadi of NCI
This is a concise tutorial dedicated for training deep learning models with pytorch and GPU on Gadi HPC of National Computational Infrastructure.

## Create Gadi account
Refer to https://opus.nci.org.au/display/Help/1.0+User+Account+and+Project+Memberships

## Transfer project to Gadi
Either though `Git clone` or SFTP software like FileZila

## Prepare environment
Gadi recommends users to compile everything on Gadi, so it is better to install python packages with following steps:
1. Load python version that you will use in your experiments. Note that the following commands is not in you `.sh` file, it should be directly typed in your bash shell.
```
module load python3/3.9.2
```
2. You can check what packages are availbale with this python version. It can be done with
```
pip list
```

3. Install packages with `pip` command and complie on Gadi. It may takes a long time, go have a coffee.
```
python3 -m pip install -v --user -r requirements.txt
```
Note that the `requirements.txt` is generated in advance. It can be generated by `pipreqs`, please refer to https://stackoverflow.com/a/68469353.

or you can directly install package by name, take `numpy` for example.
```
python3 -m pip install -v --user numpy
```
All the packages will be installed in `~/.local/lib/python3.10/site-packages`, which is only readable by yourself.

## Submit your job
1. Now you are ready for Gadi's great power. Before submit your job, be sure the python version is identical to the version that you used in your `.sh` file, since the packages you installed in *Prepare environment step 3* is only available to the python version that you loaded in *Prepare environment step 1*. Here is a example script `job.sh`.
```
#!/bin/bash

#PBS -l ncpus=12
#PBS -l mem=30GB
#PBS -l jobfs=200GB
#PBS -l ngpus=1
#PBS -q gpuvolta
#PBS -P {YOUR-PROJECT-NAME}
#PBS -l walltime=10:00:00
#PBS -l storage=gdata/{YOUR-PROJECT-NAME}+scratch/{YOUR-PROJECT-NAME}
#PBS -l wd

filename="force_47_171_190"
modelname="resnet50"
dataset="cifar10"
loss="cross_entropy"
PF_criterion="force"
PF_epoch_1=47
PF_epoch_2=171
PF_epoch_3=190
projectDIR="progressive_fixing"

module load pytorch/1.9.0
cd /scratch/{YOUR-PROJECT-NAME}/$USER/$projectDIR
python3 train.py --dataset=$dataset --model=$modelname --loss=$loss --PF_criterion=$PF_criterion --PF_epoch_1=$PF_epoch_1 --PF_epoch_2=$PF_epoch_2 --PF_epoch_3=$PF_epoch_3 >/scratch/{YOUR-PROJECT-NAME}/$USER/progressive_fixing/NCI-HPC/logs/$filename.log
```
Note that $USER is a global variable, and the pytorch version must be `pytorch/1.9.0`. This is out of the pytorch version on Gadi is binded with python version.

2. With a simple `qsub job.sh` command and your experiment can run. You can also try to monitor your job with `qstat`.

For more details, please refer to the [official help doc](https://opus.nci.org.au/display/Help/Gadi+User+Guide). Wish you luck in your research.

## Additional help scripts
If you want to submit all jobs in a folder, you can easily put the `help.sh` file in the jobs folder and run `bash ./help.sh`
