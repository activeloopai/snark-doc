# Basic Usage
Step 1. Install snark through pip3
```
$ pip3 install snark --user
```

Step 2. Go to [lab.snark.ai](https://lab.snark.ai) to sign up. Sign in through the CLI
```
$ snark login
```
It will ask you for username and password that you registered on the website.

Step 3. You can start your pod by 
```
$ snark start --pod_id  kagglepod  --pod_type tensorflow  -g 2
```
tensorflow pod has TF version 1.8.0 with cuda 9 , cudnn 7 in python3 and keras frontend. -g 2 means 2 GPUs.  You will directly login to your pod.

Other available pod types: 
 - pytorch: pytorch version 0.4.0 with cuda 9, cudnn 7 in python 3. Caffe2 is also installed in the pod
 - mxnet: mxnet version 1.0.0 with cuda 9, cudnn 7 in python 3.
 - theano: theano version 0.8.2 with cuda 8, cudnn 5 in python 3
 - caffe: caffe version 1.0 with cuda 8, cudnn 6 in python 3

Step 4. Stop the pod by
```
$ snark stop --pod_id kagglepod
```

# Persistent Storage
Note that your files will be gone when you stop your pod. Please pull data to your local dir before stopping the pod.

# Data Transfer
You can push/pull data to the pod by snark pull and snark push. 
```bash
$ snark pull --help
Usage: snark pull [OPTIONS]

  Transfer file from the pod to the localhost

Options:
  -p, --pod_id TEXT       Pod ID
  -r, --remote_path TEXT  Path to file on the Pod.
  -l, --local_path TEXT   Path to file on local machine.
  --help                  Show this message and exit.
```


```bash
$ snark push --help
Usage: snark push [OPTIONS]

  Transfer file to the pod

Options:
  -p, --pod_id TEXT       Pod ID
  -l, --local_path TEXT   Path to file on local machine.
  -r, --remote_path TEXT  Path to file on the Pod.
  --help                  Show this message and exit.
```

# Usage Monitor
Login to [lab.snark.ai](https://lab.snark.ai) to check the GPU hour used and credit left.