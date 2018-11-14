

# <img src="https://avatars3.githubusercontent.com/u/34816118?s=200&v=4" data-canonical-src="hhttps://avatars3.githubusercontent.com/u/34816118?s=200&v=4" width="40" height="40" /> Snark AI

[Update] Raw GPU access is no longer supported on the platform. Commands such as `snark start` will no longer work. Please see the docs below for the new serverless training and deployment product we have released.

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
It will ask you for username and password that you registered on the website.

**Step 3**. Create Yaml description

Snark uses yaml trainig workflow descriptions. Below is a basic MNIST example. 
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
The workflow runs as a combination of the three commands described by the file.
Save as mnist.yml

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
pip3 install https://files.pythonhosted.org/packages/e2/25/17f773c1a8c713fcc57f45a05554a4ac9ee5f0ff88418548e397eac06bb9/snark-0.2.3.1.tar.gz
```

## YAML File Properties 

Snark executes serverless ML workflows described in Yaml files. 
This section walks through properties that can be used in the Yaml file.

### Experiments
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

### Hardware
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

### Parameters

It is possible to parameterize the commands in the snark workflow yaml file.

```
  version: 1
  experiments:
    yolo:
      image: pytorch/pytorch:latest
      hardware:
        gpu: K80
        gpu_count: 2
      parameters: # describes params to be utilized in commands
        mnist_github: https://github.com/pytorch/examples
        other_param: ...
      commands: 
        - git clone {{mnist_github}}
        - cd examples/mnist
        - python main.py
``` 
Params are templated in commands using double handlebars `{{param}}`.


### Full Yaml Example
Here is the full yaml example for MNIST

```
  version: 1
  experiments:
    yolo:
      image: pytorch/pytorch 
      hardware:
        gpu: K80
        gpu_count: 2
      parameters: # describes params to be utilized in commands
        mnist_github: https://github.com/pytorch/examples
        other_param: ...
      commands: 
        - git clone {{mnist_github}}
        - cd examples/mnist
        - python main.py
```


## Usage Monitor
Login to [lab.snark.ai](https://lab.snark.ai) to check the GPU hour used and credit left.

On the cli, use `snark ps` to keep track of experiments run.
