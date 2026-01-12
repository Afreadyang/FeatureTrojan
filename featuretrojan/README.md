## FeatureTrojan

### Step 1: Download the [pretrained weights](https://openaipublic.blob.core.windows.net/diffusion/jul-2021/256x256_diffusion_uncond.pt) and copy them to [GuidedDiffusionPur/models/](GuidedDiffusionPur/models/).

### Step 2: Run [featuretrojan_cifar10.ipynb](featuretrojan_cifar10.ipynb) (using the CIFAR-10 dataset) and [featuretrojan_imagenette.ipynb](featuretrojan_imagenette.ipynb) (using the Imagenette dataset), including: training a clean model, generating poisoned data, and injecting the backdoor.



