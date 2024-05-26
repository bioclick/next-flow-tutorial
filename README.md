# What is Next Flow
NextFlow is a powerful workflow management system designed for running and managing bioinformatics data analysis pipelines. This tutorial will guide you through setting up and running a basic bioinformatics pipeline using NextFlow.

## Installation
```bash
curl -fsSL https://get.nextflow.io | bash
```
It creates the `nextflow` executable file in the current directory. You can then move it to a directory in your $PATH to run it from anywhere.

- Nextflow can also be installed from Bioconda:
```bash
conda install -c bioconda nextflow
```

## Creating a NextFlow Pipeline
### 1. Define Pipeline Steps
Open a text editor and create a new NextFlow script (e.g., `my_pipeline.nf`). Define the steps of your bioinformatics pipeline using NextFlow's domain-specific language (DSL). Each step should include inputs, outputs, and the commands to be executed.

```groovy
process stepName {
    input:
    // Define input files or directories here

    output:
    // Define output files or directories here

    script:
    """
    // Write the commands or script to execute here
    """
}
```
- `stepName:` This is a descriptive name for the step in your pipeline.

- `input:` Specify the input files or directories required for this step. You can use variables like file or directory to define inputs.

- `output:` Define the output files or directories that this step will produce. You can use the into keyword to specify where the output should be directed.

- `script:` Write the commands or script to be executed for this step. This can include any bioinformatics tools, scripts, or commands needed for the analysis.

### 2. Workflow Definition
Next, you'll define the workflow by specifying the order in which the pipeline steps should be executed. This is done using the `workflow` block:
```groovy
workflow {
    // Define the order of execution for pipeline steps here
}
```
Within the workflow block, you'll list the steps in the order they should be executed. For example:
```groovy
workflow {
    step1()
    step2()
    step3()
}
```

### 3. Input Parameters (Optional)
You can make your pipeline more flexible by defining input parameters that can be customized by the user. This is done using the `params` block:
```groovy
params {
    // Define input parameters here
}
```
For example, you can define input directories, file names, or any other variables that may vary between pipeline runs:
```groovy
params {
    inputDir = "data/"
    outputDir = "results/"
}
```
These parameters can then be used within your pipeline steps to specify input and output paths.

### 4. Running the Pipeline
To execute your NextFlow pipeline, you'll run it from the command line using the `nextflow run` command followed by the path to your pipeline script:
```bash
nextflow run my_pipeline.nf
```
NextFlow will automatically manage the execution of your pipeline steps, handling task dependencies, parallel execution, and resource management.

### 5. Monitor Progress
NextFlow provides real-time monitoring of pipeline execution, allowing you to track progress, resource usage, and potential issues directly from the command line. You'll see status updates and logs as each step of the pipeline is executed.


## Flags and Options
NextFlow provides a rich set of options and flags that you can use to control various aspects of its execution, from running the pipeline to configuring resource management and logging.

### Running a Pipeline
```bash
nextflow run <pipeline script> [options]
```
- `<pipeline script>:` The NextFlow script to be executed. This can be a local file or a script from a GitHub repository (e.g., nextflow run user/repo).

### Common Options
#### 1. General Options
- `-C <config_file>:` Use the specified configuration file. Can be used multiple times to load multiple config files.

- `-D <key=value>:` Set a system property. Useful for overriding settings in the config files.

- `-N:` Disable ANSI colors in the terminal output.
- `-bg:` Run NextFlow in the background.

#### 2. Pipeline Parameters
- `--<param_name> <value>:` Define a parameter value to be used within the pipeline script. For example: --reads 'data/*.fq'.

#### 3. Execution Control
- `-c <config_file>:` Use the specified configuration file.
- `-with-dag <file>:` Generate a Directed Acyclic Graph (DAG) image of the pipeline execution plan.
- `-with-timeline <file>:` Generate an execution timeline report.
- `-with-trace <file>:` Generate a task execution trace report.
- `-with-report <file>:` Generate an execution report with runtime metrics.
- `-with-weblog <url>:` Send execution progress to the specified URL.
- `-with-notification <url>:` Send pipeline completion notifications to the specified URL.

#### 4. Resource Management
- `-qs <size>:` Set the task queue size (number of tasks to be queued).
- `-resume:` Resume the execution of a previously run pipeline from where it left off.
- `-latest:` Resume the most recent execution.
- `-profile <profile>:` Apply the specified configuration profile.

#### 5. Logging and Debugging
- `-log <file>:` Specify the file where logs should be written.
- `-ansi-log:` Enable ANSI color logging in the log file.
- `-debug:` Enable debug mode.
- `-trace:` Enable trace logging mode.
- `-silent:` Suppress standard output.
- `-quiet:` Produce minimal console output.
- `-v:` Print the NextFlow version.

### Configuring a Pipeline
#### nextflow.config
The nextflow.config file is used to configure various aspects of pipeline execution, such as resource allocation, process execution, and environment settings.
```groovy
params {
    reads = "data/*.fq"
    reference = "data/reference.fa"
    outdir = "results"
}

process {
    executor = 'local'
    memory = '4 GB'
    cpus = 2
    time = '2h'
}

docker {
    enabled = true
    runOptions = '-u $(id -u):$(id -g)'
}

executor {
    queueSize = 100
}

env {
    PATH = "$baseDir/bin:$PATH"
}
```
#### Configuration Profiles
Profiles allow you to define different configurations for different environments (e.g., local, cluster, cloud).

```groovy
profiles {
    local {
        process.executor = 'local'
    }
    cluster {
        process.executor = 'slurm'
        process.queue = 'bioinformatics'
        docker.enabled = true
    }
    cloud {
        process.executor = 'awsbatch'
        aws.region = 'us-east-1'
        docker.enabled = true
    }
}
```
### Environment Variables
NextFlow supports several environment variables to control its behavior:

- `NXF_DEBUG:` Enable debug mode.
- `NXF_VERBOSITY:` Set the verbosity level of logging.
- `NXF_ANSI_LOG:` Enable or disable ANSI colors in logs.
- `NXF_OPTS:` Set default JVM options.
- `NXF_HOME:` Define the NextFlow installation directory.
- `NXF_WORK:` Specify the directory where temporary files are stored.

### Process Configuration
Each process in NextFlow can be configured individually within the script or the `nextflow.config` file:
```groovy
process myProcess {
    cpus = 4
    memory = '8 GB'
    time = '6h'
    container = 'mydocker/image:latest'
    errorStrategy = 'retry'
    maxRetries = 3
    maxErrors = '-1'
    scratch = true
}
```


