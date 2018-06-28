
# <img src="https://avatars3.githubusercontent.com/u/34816118?s=200&v=4" data-canonical-src="hhttps://avatars3.githubusercontent.com/u/34816118?s=200&v=4" width="40" height="40" /> Snark AI

## Basic Usage
**Step 1**. Install snark through pip3
```
pip3 install snark --user
```

**Step 2**. Go to [hub.snark.ai](https://hub.snark.ai) to sign up then click on username and choose `Get API Key`


**Step 3** open Python and write
``` python
import numpy as np
import snark

snark.auth(token=’YOUR_AUTHENTICATION_TOKEN’)

alexnet = snark.model(model_name=’snarkai/AlexNet:latest')
output = alexnet.infer(np.zeros([1, 3, 224, 224]))
```
