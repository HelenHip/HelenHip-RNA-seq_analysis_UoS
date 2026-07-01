<img src="Images/ShefLogo.png"
     alt="Sheffield University Icon"
     style="left; margin-right: 10px;" />
<img src="Images/NEOF.png"
    alt="NEOF Icon"
    style="left; margin-right: 10px;" />
<br>
<br>
## RNA-seq pipeline workshop for Stanage
<br>
<font size="4">

<details>
<summary>

### 1) Credits

</summary>
  <br>
  <br>
  Compiled by: Helen Hipperson, University of Sheffield<br>
  <br>
  This pipeline is set up to perform the analysis of RNAseq data in the context of examining differential 
  expression, using standard tools, as introduced in the NEOF RNAseq, Differential Gene Expression and 
  Pathway Analysis workshop. Details of the workshop can be found [here](https://neof.org.uk/training/). 

  This pipeline closely follows the approach and workflow developed for the above workshop by Katy Maher, Helen 
  Hipperson, Ewan Harney, Steve Paterson, Bert Overduin, Matthew Gemmell and Xuan Liu. It also relies substantially 
  on scripts developed by Katy Maher. Use and referencing of this repository implicitly acknowledges these contributions.
</details>

<br>

<details>
<summary>

### 2) About

</summary>
  <br>
  <br>
  
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
  <br><br>
 Contact: Helen Hipperson //  h.hipperson@sheffield.ac.uk
</details>
<summary>

### 3) Getting started on the HPC

</summary>
  <br>

#### 3.1) Access the HPC

  <br>
	
  To access the Stanage HPC you must be connected securely to the university network - this can 
  be achieved remotely by using the virtual private network (VPN) service.

  Please see the university IT pages for details on how to [connect to the VPN](https://students.sheffield.ac.uk/it-services/vpn).

  Once connected to the VPN you also need to connect to the HPC using a secure shell (SSH)
  connection. This can be achieved using the command line on your system (the Mac Terminal or Windows 
  PowerShell) or a software package such as [MobaXterm](https://mobaxterm.mobatek.net/).

  <br>

#### 3.2) Access a worker node on Stanage

  <br>
  
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

  
  <br>
#### 3.3) Load the Genomics Software Repository
  <br>
  
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

  
  <br>
  #### 3.4) Set up your conda profile
  <br>
  
  If you have never run conda before on Stanage, you might have to initialise your conda. To do this type:
  
  ```
  conda init bash
  ```
  
  You will then be asked to reopen your current shell. Log out and then back into Stanage and then continue. 
  <br>
  
  <br>
  #### 3.5) Running scripts on the HPC cluster
  <br>
  
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


  <br>
  #### 3.6) Passing command line arguments to a script
  <br>

  As well as running the standardised scripts there are some parameters which will be unique to your project or data. 
  For example, these might be your file extensions, the name of your reference genome or the filtering parameters you
  want to implement for quality control (QC).<br>
  <br>

  To run a script with these extra parameters (termed 'arguments') we supply them on the command line with a 'flag'.
  For example, you might supply your genome file name to a script using the '-g' flag as

  ```
  a_demo_script.sh -g my_orgamism.fa
  ```