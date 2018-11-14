

## Basic Usage
**Step 1**. Install snark through pip3
```
pip3 install snark
```
In case of difficulties with installation, take a look at the [troubleshooting](#troubleshooting) section. 

**Step 2**. Go to [lab.snark.ai](https://lab.snark.ai) to sign up. Sign in through the CLI
```
snark login
```

**Step 3**. Create Yaml description

Snark uses yaml training workflow descriptions. Below is a basic MNIST example. 
```
  version: 1
  experiments:
    mnist:
      image: pytorch/pytorch:latest
      hardware:
        gpu: k80
      command:
        - git clone https://github.com/pytorch/examples
        - cd examples/mnist
        - python main.py
```
This yaml file describes an `mnist` experiment which uses _pytorch_ on a single k80 GPU.
The workflow runs as a combination of three commands described by the file.
Save as `mnist.yml`.

**Step 4**. Start the workflow

Use `snark up` to start the workflow.
```
snark up -f mnist.yml
```

Snark up starts the experiment described Yaml file. It will spin up a cluster with 1 k80 GPU.
Workflows can be of different nature: sing GPU training, distributed training, hyperparameter search.

**Step 5** List Experiments

`snark ps` command will list the experiments along with their ids and states.
```
  snark ps
```
Experiment Ids are to be used to tear down the workflows.

**Step 6** Tear Down experiments
Snark workflows can be torn down using `snark down` command
```
  snark down {experiment_id}
```
snark down shuts down all cloud resources utilized by the workflow.

## Monitoring
Login to [lab.snark.ai](https://lab.snark.ai) to check the GPU usage and credits left.

### Experiments
On the cli, use `snark ps` to keep track of all experiment.
```
snark ps
```

### Tasks
To check the state of each task per experiment run.
```
snark ps {experiment_id}
```

### Logs
To get logs of running experiments 
```
snark logs {experiment_id}
```

## Properties 

Snark executes serverless ML workflows described in Yaml files. 
This section walks through properties that can be used in the Yaml file.

### Multiple Experiments
Snark executes experiments described in the Yaml file. Those experiments are ML workflows such as model training, hyper parameter search etc.

Here is the way to declare them in the Yaml file:
```
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
_Currently only public images are accepted_
```
  version: 1
  experiments:
    yolo:
      image: pytorch/pytorch # if no tag is provided, snark will pull :latest
```

### Multiple GPUs
This property describes the hardware to run the experiment on. 
```
  version: 1
  experiments:
    yolo: # experiment name
      image: pytorch/pytorch 
      hardware:
        gpu: K80
        gpu_count: 2
```
`gpu` is the name of the gpu to use
`gpu_count` describes the number of gpus for the given experiment to run on. By default it's value is **1**.

Supported GPUs are K80 and V100 and the supported `gpu_count`s for them are **(1, 8, 16)** and **(1, 4, 8)** respectively


### Commands
Snark Workflows comprise of commands. The commands are executed against the docker image provided.
```
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
The above example demonstrates how snark workflows get executed: using provided image and executing the given commands on that image.


### Hyperparameter search
It is possible to sample from discrete or continous range of parameters. You would need to provide the sampling method, number of samples, number of parallel workers and variables in the command execution list. Params are templated in commands using double handlebars `{{param}}`.

```
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
```
sudo pip3 install snark
```
#### If you don't have `sudo` access:
```
pip3 install snark --user
```
**AND** add the following to your `~/.bashrc` file:
```
export PY_USER_BIN=$(python3 -c 'import site; print(site.USER_BASE + "/bin")')
export PATH=$PY_USER_BIN:$PATH
```
**AND** reload your `~/.bashrc`:
```
source ~/.bashrc
```
#### Snark Not Found
If you tried all above and still get `snark not found` error message, try:
1) Updating your pip3 through `pip3 install --upgrade pip3`
2) Try installing specific version through tarball:
```
pip3 uninstall snark
pip3 install https://files.pythonhosted.org/packages/6b/c4/1112f032a3d90686d757e5b0b325564a047488fc74fa43a138148dc2b8a5/snark-0.3.2.0.tar.gz
```

In case of questions or issues please contact us at support@snark.ai
