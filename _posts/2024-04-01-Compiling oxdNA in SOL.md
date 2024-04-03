---
layout: post
title: Compiling oxDNA in SOL
date: 2024-04-01 00:00:00
description: Compiling oxDNA in SOL step-by-step 
tags: xyz
categories: sample-posts
giscus_comments: false
featured: true
---

Below is a brief procedure to compile oxDNA in SOL 

- Go to home, create folder `github`, Clone git repo for oxDNA from `https://github.com/Subhajit-Roy-Partho/oxDNA.git`

### Using tmux to run jobs 

- Create `empty.sh` and paste 
```bash
#!/bin/sh
#SBATCH -q private
#SBATCH -p general
#SBATCH -G a100:1
#SBATCH -t 5-00:00
#SBATCH -c 8
#SBATCH -o code.out
#SBATCH -e code.err
exec screen -Dm -S slurm$SLURM_JOB_ID
```
Then run `sbatch empty.sh`, this will provide you a session such as `go20`

- Go inside the session by `ssh g020`

- Use `tmux` for running jobs, refer to `https://tmuxcheatsheet.com/`
Ctrl+b, 0 (switch )
Ctrl+b, c (Create new window)
Ctrl+attach (Go to tmux)
tmux attach (go inside tmux session)
Ctrl+b, d (Get out of tmux session)

### Compile oxDNA 

- Go back to the location where oxDNA is installed inside the tmux session, mkdir `build` and run `cmake .. -DCMAKE_BUILD_TYPE=Release`, then type `j8` to use 8 CPU for compilation

- Check `bin` to make sure oxdna folder is added to PATH, use `vim ~/.bashrc` to check the folder
It should have the `$PATH:/home/ddeeksha/Software:/home/ddeeksha/github/oxDNA/build/bin`

- Go back to `scratch/ddeeksha/` and prepare a folder to store all jobs such as mkdir `Simulation`

### Transfer folder to sol 

- To send a folder or file into sol `sol send filename /scratch/ddeeksha`

- oxDNA requires three files to run:
```bash
input(input to MD)
oxDNA2_sequence_dependent_parameters (oxDNA parameters)
submit.sh (running script)
inputMC (input)
inputProd (final inpurProd file)
```

### Running a Simulation 

- Use to run simulation `oxDNA inputMC; oxDNA input, oxDNA inputProd`

### To get RMSF mean 

- Use command `oat mean -p 20 -o mean.dat -d rmsf.json trajectory.dat`

### To get backbone strength 

- Use command `oat backbone_flexibility -o out.json -d flex.dat input.top trajectoryProd.dat`

### Miscellaneous

- To check all jobs `myjobs`
- To kill a a job `scancel jobID`

### Conclusion



### References

https://lorenzo-rovigatti.github.io/oxDNA/install.html
https://github.com/Subhajit-Roy-Partho
https://www.markdownguide.org/cheat-sheet/