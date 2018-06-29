
# <img src="https://avatars3.githubusercontent.com/u/34816118?s=200&v=4" data-canonical-src="hhttps://avatars3.githubusercontent.com/u/34816118?s=200&v=4" width="40" height="40" /> Snark AI

## Basic Usage
**Step 1**. Install snark through pip3
```
pip3 install snark --user
```
If you're having difficulties with installation, take a look at the [troubleshooting](#troubleshooting) section. 

**Step 2**. Go to [hub.snark.ai](https://hub.snark.ai) to sign up. Then run:
```
snark login
```


**Step 3** open Python and write
``` python
import numpy as np
from snark.infer import model

alexnet = model(model_name='snarkai/AlexNet:latest')
output = alexnet.infer(np.zeros([1, 3, 224, 224]))
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
