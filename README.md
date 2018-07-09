
# <img src="https://avatars3.githubusercontent.com/u/34816118?s=200&v=4" data-canonical-src="hhttps://avatars3.githubusercontent.com/u/34816118?s=200&v=4" width="40" height="40" /> Snark AI

## Basic Usage
**Step 1**. Install snark through pip3
```
pip3 install snark
```
If you're having difficulties with installation, take a look at the [troubleshooting](#troubleshooting) section. 

**Step 2**. Go to [lab.snark.ai](https://lab.snark.ai) to sign up. Sign in through the CLI
```
snark login
```
It will ask you for username and password that you registered on the website.

**Step 3**. You can start your pod by 
```
snark start
```
This will create a pod with default configuration. By default, your pod will have one P106 GPU and will have pytorch preinstalled. You can create a custom pod by specifying the following parameters:
```
  -t, --pod_type [pytorch|tensorflow|theano|mxnet|caffe]
                                  Pody type (['pytorch', 'tensorflow',
                                  'theano', 'mxnet', 'caffe'])
  -g, --gpu_count INTEGER         Number of GPUs
  -s, --gpu_spec [P106|1080]  GPU type. Choose one of: ['P106',
                                  '1080']
```
For example, the following command creates a Tensorflow pod with 2 P106 GPUs. The pod is named "tf_pod".
```
snark start tf_pod --pod_type tensorflow  -g 2
```

You will have sudo access with password `admin`. We use key-based authentication and block password login to your pod so no need to worry about the weak password. 

Our available pod types:  
 - **tensorflow/keras**: tensorflow version 1.8.0 with cuda 9, cudnn 7 in python3. Keras is installed in the pod.
 - **pytorch/caffe2**: pytorch version 0.4.0 with cuda 9, cudnn 7 in python 3. Caffe2 is also installed in the pod
 - **mxnet**: mxnet version 1.0.0 with cuda 9, cudnn 7 in python 3.
 - **theano**: theano version 0.8.2 with cuda 8, cudnn 5 in python 3
 - **caffe** : caffe version 1.0 with cuda 8, cudnn 6 in python 3

**Step 4**. Stop the pod by
```
snark stop my_pod
```


**List active pods**

Use `snark ls` to list your active pods.

**Reconnect to an active pod**

To reconnect to an active pod, execute an `attach` command and specify the id of the pod that you want to resume.
```
snark attach my_pod
```

**Change pod hardware characteristics**
Currently, each pod is tied to the GPU time it was first run on. If you want to try to try different GPU types, change the pod_id:
```
snark start tf_pod2 --pod_type tensorflow  --gpu_spec 1080
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
pip3 install https://files.pythonhosted.org/packages/53/77/d3844b92e626f673cb86c7498a1dff10c76b47593a342729e50cff9af8f3/snark-0.2.1.8.tar.gz
```
## File Transfer
You can push/pull data to the pod by snark pull and snark push. Use it as a convenient replacement for `scp`. 


### Download Files
```bash
snark pull my_pod -r /path/to/remote/file.tar.gx -l /path/to/local/file.tar.gx
```
```bash
$ snark pull --help
Usage: snark pull [OPTIONS]

  Transfer file from the pod to the localhost

Options:
  -r, --remote_path TEXT  Path to file on the Pod.
  -l, --local_path TEXT   Path to file on local machine.
  --help                  Show this message and exit.
```

### Upload Files
```bash
snark push my_pod -r /path/to/remote/file.tar.gx -l /path/to/local/file.tar.gx
```
```bash
$ snark push --help
Usage: snark push [OPTIONS]

  Transfer file to the pod

Options:
  -l, --local_path TEXT   Path to file on local machine.
  -r, --remote_path TEXT  Path to file on the Pod.
  --help                  Show this message and exit.
```
Note: if you want use `~'` or `\*` when specifying remote file path, please take them in quotes. Eg:
```
snark push kaggle_pod -r "~/test.txt" -l test.txt
```

## Persistent Storage
Each pod comes with 10GB of persistant storage. This storage will be destroyed only when you use `snark terminate` to destroy your pod. 

If you would like a pod with more persistant storage, shoot us an email at *support@snark.ai*. 

Common datasets from Kaggle competitions and more are accessible (read only) at `/datasets`. If there's a dataset you would like to add/request, reach out to us at *support@snark.ai*.

## Usage Monitor
Login to [lab.snark.ai](https://lab.snark.ai) to check the GPU hour used and credit left.
