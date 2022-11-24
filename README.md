# Para_sweep_CC
This is a demo of how to fulfill parallel hyperparameter training on Compute Canada. Here, I use META for parallel training.
## META setting for Compute Canada
1. Login in Cedar and ``cd`` to the project folder.
2. Load the ```meta-farm``` module: 
    ```
    $ module load meta-farm
    ```
3. Choose a name for farm directory, e.g. ```para```, and create it with command:
    ```
    $ farm_init.run  para
    ```
4. Then ```cd para``` and ```ls```, you will see files of ```config.h, job_script.sh, resubmit_script.sh, single_case.sh, table.dat```
5. ```job_script.sh``` is for slurm commands, ;ile your account information and how much source you want to use. For example, you could edit it in this way:
    ```
    #!/bin/bash
    # Here you should provide the sbatch arguments to be used in all jobs in this serial farm
    # It has to contain the runtime switch (either -t or --time):
    #SBATCH -t 0-00:10 # 10 minutes training for all nodes
    #SBATCH --mem=4G # 4G memory for each node
    #  You have to replace Your_account_name below with the name of your account:
    #SBATCH --account= $your_supervisor_name
    #SBATCH --mail-user= $your_email_to_be_notified
    #SBATCH --mail-type=ALL
    source ../$ENV/bin/activate # activate the virtual environment for your code
    # Don't change this line: #don't edit afterwards
    task.run
    ```
6. ```single_case.sh``` needs not to be changed this case.
7. ```table.dat``` is for your job arrangement. You need to write command line commands here. For this demo, in order to generate/modify ```table.dat```, the following commands could be applied:
    ```
    for ((i=1;i<=2;i++)); do echo python $your_folder_path/test01.py; done > table.dat
    ```
    Then the command line will be written into ```table.dat```. In this demo, I only use two nodes for parallel training. Commands like ```cp``` and ```mkdir``` and etc can also be written here.
8. If there comes up with error of ```permission denied```, you could run ```chmod 777``` for the file to give the system right to run it.
### Useful link:
1. [document from Compute Canada](https://docs.alliancecan.ca/wiki/META:_A_package_for_job_farming#single_case.sh)
2. [very useful webinar from Youtube for above all](https://www.youtube.com/watch?v=GcYbaPClwGE)
## Sweep from Wandb
1. In your virtual environment,
    ```
    pip install wandb
    wandb login
    ```
    you need to paste your authentication code this step, just follow the instruction from code. 
2. Define your sweep configuration in ```sweep_configuration``` in ```test01.py```.
3. Don't forget ```wandb.log``` in your code.
4. You will see the real time result in [your wandb web page](https://wandb.ai)
