

# <img src="https://avatars3.githubusercontent.com/u/34816118?s=200&v=4" data-canonical-src="hhttps://avatars3.githubusercontent.com/u/34816118?s=200&v=4" width="40" height="40" /> Snark AI

## Basic Usage
**Step 1**. Install snark with pip3
```bash
  pip3 install snark
```
In case of difficulties with installation, take a look at the [troubleshooting](#troubleshooting) section. 

**Step 2**. Go to [lab.snark.ai](https://lab.snark.ai) to sign up. Sign in through the CLI
```bash
  snark login
```

**Step 3**. Create Yaml description

Snark uses yaml training workflow descriptions. Below is a basic MNIST example. 
```yaml
  version: 1
  experiments:
    mnist:
      image: pytorch/pytorch:latest
      hardware:
        gpu: K80
      command:
        - git clone https://github.com/pytorch/examples
        - cd examples/mnist
        - python main.py
```
This yaml file describes an `mnist` experiment which uses _pytorch_ on a single _K80 GPU_.
The workflow runs as a combination of three commands described by the file.
Save as `mnist.yml`.

**Step 4**. Start the workflow

Use `snark up` to start the workflow.
```bash
  snark up -f mnist.yml
```

Snark up starts the experiment described by the yaml file. It spins up a cluster with 1 K80 GPU.
In general workflows executed by snark can be of different nature:
- single-GPU ML training 
- distributed ML training 
- hyperparameter search

**Step 5** List Experiments

`snark ps` command will list the experiments along with their ids and states.
```bash
  snark ps
```
Experiment Ids are used to tear down the workflows.

**Step 6** Tear Down Experiments
Snark workflows can be torn down using `snark down` command
```bash
  snark down {experiment_id}
```
The `snark down` command shuts down all cloud resources utilized by the experiment workflow.


## Monitoring
Login to [lab.snark.ai](https://lab.snark.ai) to check the GPU usage and credits left.

### Experiments
On the cli, use `snark ps` to keep track of all experiments.
```bash
  snark ps
```

### Tasks
To check the state of each task per experiment run.
```
snark ps {experiment_id}
```

### Logs
To get logs of running experiments 
```bash
  snark logs {experiment_id}
```

## Properties 

Snark executes serverless ML workflows described in Yaml files. 
This section walks through properties that can be used in the Yaml file.

### Multiple Experiments
Snark executes experiments described in Yaml file. Those experiments are ML workflows such as model training, hyper parameter search etc.

Here is how to declare them in the Yaml file:
```yaml
  version: 1
  experiments:
    yolo: # the experiment name
      # here go the details of the workflow
      ...
    my_second_experiment:
      # description of another experiment
      ...
    third_experiment:
      ...

```
`snark ps` will show the experiment ids and states after they are run.


### Docker Image

Snark executes the workflow commands against the given docker image.
**_Note:_** _Currently only public images are accepted_
```yaml
  version: 1
  experiments:
    yolo:
      image: pytorch/pytorch # if no tag is provided, snark will pull :latest image
```

### Multiple GPUs
This property describes the hardware to run the experiment on. 
```yaml
  version: 1
  experiments:
    yolo:
      image: pytorch/pytorch 
      hardware:
        gpu: K80 # use K80 GPU
        gpu_count: 2 # use 2 K80 GPUs
```
`gpu` is the name of the gpu to use
`gpu_count` describes the number of gpus for the given experiment to run on. By default it's value is **1**.

Supported GPUs are K80 and V100 and the supported `gpu_count`s for them are **(1, 8, 16)** and **(1, 4, 8)** respectively


### Commands
Snark Workflows comprise of commands. The commands are executed against the docker image provided.
```yaml
  version: 1
  experiments:
    yolo:
      image: pytorch/pytorch 
      hardware:
        gpu: K80
        gpu_count: 2
      commands: # Commands is an array of commands to execute against the image declared above
        - git clone https://github.com/pytorch/examples
        - cd examples/mnist
        - python main.py
```
The above example demonstrates how snark workflows are executed: using provided image and running the given commands on that image.


### Hyperparameter search
It is possible to sample from discrete or continous range of parameters. You would need to provide the sampling method, number of samples, number of parallel workers and variables in the command execution list. Params are templated in commands using double handlebars `{{param}}`.

```yaml
version: 1
experiments:
  mnist_hyperparam_search:
    image: pytorch/pytorch:latest
    parameters:
      github: https://github.com/pytorch/examples
      batch_size: [32,64,128,256]
      lr: "0.01-0.09"
    hardware:
      gpu: k80
      gpu_count: 1
    sampling: 'random'
    samples: 8
    workers: 4
    command:
      - git clone {{github}} && cd examples/mnist
      - python main.py --batch-size {{batch_size}} --lr {{lr}}
```


<a name="troubleshooting"></a>
### Troubleshooting

#### If you get a `Permission denied` message:
```bash
  sudo pip3 install snark
```
#### If you don't have `sudo` access:
```bash
  pip3 install snark --user
```

**AND** add the following to your `~/.bashrc` file:
```bash
  export PY_USER_BIN=$(python3 -c 'import site; print(site.USER_BASE + "/bin")')
  export PATH=$PY_USER_BIN:$PATH
```
**AND** reload your `~/.bashrc`:
```bash
  source ~/.bashrc
```
#### Snark Not Found
If you tried all above and still get `snark not found` error message, try:
1) Updating your pip3 through `pip3 install --upgrade pip3`
2) Try installing specific version through tarball:
```bash
  pip3 uninstall snark
  pip3 install https://files.pythonhosted.org/packages/6b/c4/1112f032a3d90686d757e5b0b325564a047488fc74fa43a138148dc2b8a5/snark-0.3.2.0.tar.gz
```

In case of questions or issues please contact us at support@snark.ai

