<img src="Images/ShefLogo.png"
     alt="Sheffield University Icon"
     style="left; margin-right: 10px;" />
<img src="Images/NEOF.png"
    alt="NEOF Icon"
    style="left; margin-right: 10px;" />
<br>
<br>
# RNA-seq pipeline workshop for Stanage
<br>
<font size="4">

## Introduction

<details>
<summary>

### 1) Credits

</summary>
 
  Compiled by: Helen Hipperson, University of Sheffield<br>
 
  This pipeline is set up to perform the analysis of RNAseq data in the context of examining differential 
  expression, using standard tools, as introduced in the NEOF RNAseq, Differential Gene Expression and 
  Pathway Analysis workshop. Details of the workshop can be found [here](https://neof.org.uk/training/). 

  This pipeline closely follows the approach and workflow developed for the above workshop by Katy Maher, Helen 
  Hipperson, Ewan Harney, Steve Paterson, Bert Overduin, Matthew Gemmell and Xuan Liu. It also relies substantially 
  on scripts developed by Katy Maher. Use and referencing of this repository implicitly acknowledges these contributions.
</details>


<details>
<summary>

### 2) Introduction

</summary>
  
  
  
  The pipeline relies on you having short-read Illumina transcriptomic data for your samples, and access to a 
  published or publically-available reference genome or transcriptome with (preferably) a high level of annotation.

  This protocol has been written for use with the University of Sheffield's
  [Stanage](https://docs.hpc.shef.ac.uk/en/latest/stanage/index.html#gsc.tab=0) high-performance computing (HPC) 
  system, but should be applicable to any GNU/Linux-based HPC system, with appropriate environments, software 
  installations and modification. Your mileage may vary.

  Code which the user (that's you) must run is highlighted in a code block like this:
  ```
  I am code - you must run me
  ```
  Sometimes the desired output from a command or additional information about the command is included in the code
  block as a comment.
  
  For example:
  ```
  Running this command
  # Should produce this output
  ```

  File-paths within normal text are within single quote marks, like this:

  '/home/user/a_file_path'
  
 Contact: Helen Hipperson //  h.hipperson@sheffield.ac.uk
</details>



<details>
<summary>

### 3) Getting started on the HPC

</summary>

<details>
<summary>

#### 3.1) Access the HPC

 </summary>
 
	
  To access the Stanage HPC you must be connected securely to the university network - this can 
  be achieved remotely by using the virtual private network (VPN) service.

  Please see the university IT pages for details on how to [connect to the VPN](https://students.sheffield.ac.uk/it-services/vpn).

  Once connected to the VPN you also need to connect to the HPC using a secure shell (SSH)
  connection. This can be achieved using the command line on your system (the Mac Terminal or Windows 
  PowerShell) or a software package such as [MobaXterm](https://mobaxterm.mobatek.net/).
</details>
  
  
  <details>
  <summary>

#### 3.2) Access a worker node on Stanage

 </summary>
 
  
  Once you have successfully logged into Stanage, you need to access a worker node:

  ```
  srun --pty bash -l
  ```
  You should see that the command prompt has changed from

  ```
  [<user>@login1 [stanage] ~]$
  ```
  to
  ```
  [<user>@node001 [stanage] ~]$
  ```
  ...where \<user\> is your University of Sheffield (UoS) IT username.
</details>
  
  
   <details>
  <summary>
  
#### 3.3) Load the Genomics Software Repository

</summary>
  
  
  The Genomics Software Repository contains several pre-loaded pieces of software
  and environments useful for a range of genomic analyses, including this one.
  
  Type:
  
  ```
  source ~/.bash_profile
  ```
  
  Did you receive the following message when you accessed the worker node?
  
  ```
  Your account is set up to use the Genomics Software Repository
  ```

  If so, you are set up and do not need to do the following step.
  
  If not, enter the following:
  
  ```
  echo -e "if [[ -e '/mnt/community/Genomics' ]];\nthen\n\tsource /mnt/community/Genomics/.bashrc\nfi" >>
$HOME/.bash_profile
  ```
  Then re-load your profile:
  
  ```
  source ~/.bash_profile
  ```
  
  Upon re-loading, you should see the above message relating to the Genomics Software Repository.
</details>
  
  
   <details>
  <summary>
  
#### 3.4) Set up your conda profile
 
 
 </summary>
  
  
  If you have never run conda before on Stanage, you might have to initialise your conda. To do this type:
  
  ```
  conda init bash
  ```
  
  You will then be asked to reopen your current shell. Log out and then back into Stanage and then continue. 
</details>
  
  
  
  
   <details>
  <summary>
  
#### 3.5) Running scripts on the HPC cluster

 </summary>
  
  
  To add our job to the job scheduler, we would submit the shell scripts using 'sbatch'
  (don't do this; it's simply an example).

  ```
  ## EXAMPLE - DON'T RUN
  sbatch scripts/example_script.sh
  ```

  We could then view the job that we have submitted to the job queue using 'squeue'.

  ```
  squeue --me
  ```

  The job will then receive the allocated resources, the task will run, and the appropriate output files will be generated 
  (inlcuding output and error logs). In the following workflow, the output from a particular step is generally the input
  for the next step. **IMPORTANT:** You'll need to wait for each job to finish before submitting the next. It is also
  **important** to check the error and output logs after each step/job (before launching the next job) to see whether 
  it has completed properly or if there were issues or failures.
  
  You should also keep in mind that the resources (the number of cores, memory and time) requested in the scripts 
  may not be suitable for your own data set and analysis (or another HPC, if you are not using Stanage), and may 
  need to be changed. Again, the NEOF Bioinformatics Team can help in setting these in the scripts.
</details>

  
   <details>
  <summary>
  
#### 3.6) Passing command line arguments to a script

   </summary>
  

  As well as running the standardised scripts there are some parameters which will be unique to your project or data. 
  For example, these might be your file extensions, the name of your reference genome or the filtering parameters you
  want to implement for quality control (QC).
  

  To run a script with these extra parameters (termed 'arguments') we supply them on the command line with a 'flag'.
  For example, you might supply your genome file name to a script using the '-g' flag as

  ```
  a_demo_script.sh -g my_organism.fa
  ```
  </details>
  
  <details>
  <summary>
  
#### 3.7) Accessing the example data

   </summary>
   
   
   <details>
  <summary>
  
##### 3.7.1) Set up

   </summary>
   
  You should work in the directory '/mnt/parscratch/users/' on Stanage as this allows adequate space for your data and
  output (opposed to your home directory) and it also allows shared access to your files, scripts, and output and error
  logs, all of which are useful for troubleshooting.

  Check if you already have a directory in '/mnt/parscratch/users/' by running the command exactly as it appears below.

  ```
  ls /mnt/parscratch/users/$USER
  ```

  If you receive the message
  ```
  ls: cannot access /mnt/parscratch/users/<user>: No such file or directory
  ```
  you'll need to create a new folder in '/mnt/parscratch/users/' using the command exactly as it appears below:

  ```
  mkdir -m 0755 /mnt/parscratch/users/$USER
  ```

  Great! Now you have your own folder to work in on the hpc.
</details>

   <details>
  <summary>
  
##### 3.7.2) Getting the data

   </summary>
   
   
   Before we can start we first need to make a directory which will be used to contain all the files you generate throughout this workshop.

To do this type the following commands.

```{bash eval=FALSE}
#Make sure you are in your parscratch user directory
cd /mnt/parscratch/users/$USER
```

Then copy the directory with the example data into your 'home' directory.

```{bash eval=FALSE}
cp -r /mnt/parscratch/users/bo1hxh/public/rnaseq .
```


You will need to activate the rnaseq conda environment before continuing. Carry this out with the following command.

```{bash eval=FALSE}
conda activate rnaseq
```

You're now ready to start the analyses!
  
  </details>
  
  </details>

  </details>

  
  
## Generating gene counts

<details>
<summary>

### 4) Quality Control

</summary>

This tutorial will give hands on experience with quality control of transcriptomic Illumina data. We will first look at the quality of the data.

</details>



<details>
<summary>

#### 4.1) Workshop data

</summary>

Before we can carry on with the workshop we need to change into the directory containing the raw data for two _Drosophila_ samples. 

<center>
![](figures/Dros_2.png){style="width:200px; border-radius:15px"}
</center>
```{bash eval=FALSE}
cd /mnt/parscratch/users/$USER/rnaseq/Practical_one
```

List the contents of the current directory to confirm the four fastq files are there (the files ending in `.fq.gz`).
```{bash eval=FALSE}
ls
```

Have a look at the structure of the input fastq file:
```{bash eval=FALSE}
zcat 17_slice_R1.fq.gz | head 
```

These fastq files are compressed (with the .gz file extension). `zcat` is a command for viewing the contents of compressed files, the pipe `|` symbol then passes this to `head` to display only the first few lines of the file.

Fastq files contain a header line, the nucleotide sequence, and its corresponding quality scores.
</details>

<details>
<summary>

#### 4.2) Quality assessment

</summary>

</details>

<details>
<summary>

#### 4.3) Quality control

</summary>

</details>

<details>
<summary>

### 5) Aligning Illumina transcriptome data to a reference genome

</summary>

</details>

<details>
<summary>

### 6) Counting Reads using HTSeq-Count

</summary>

</details>


## Differential Gene Expression

<details>
<summary>

### 7) Introduction

</summary>

</details>

<details>
<summary>

### 8) Filter the data

</summary>

</details>

<details>
<summary>

### 9) DGE analysis

</summary>

</details>

<details>
<summary>

### 10) Visualising the results

</summary>

</details>

<details>
<summary>

### 11) Export the results

</summary>

</details>