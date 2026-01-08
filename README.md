
## Repository is still under construction; please wait a few more weeks.

## Dependency

This repository was developed with PyTorch 1.12.1, and should be compatible with PyTorch of newer versions. To set up the required environment, first manually install PyTorch with CUDA, and then install other packages via `pip install -r requirement.txt`.


### FeatureTrojan

See [featuretrojan/](featuretrojan/)

### Baseline Backdoor Attacks

- See [poison_tool_box/](poison_tool_box/) and [create_poisoned_set.py](create_poisoned_set.py) (`none`, `badnet`, `blend`, `trojan`, `clean_label`, `SIG`, `dynamic`, `WaNet`, `TaCT`, `adaptive_patch`, `adaptive_blend`)

- See [LOTUS/](LOTUS/) (`LOTUS`)

### Backdoor Defenses

- See [cleansers_tool_box/](cleansers_tool_box/) and [cleanser.py](cleanser.py) (`SS`, `AC`, `Strip`, `SCAn`, `Frequency`, `SPECTRE`)

- See [other_defenses_tool_box/](other_defenses_tool_box/) and [other_defense.py](other_defense.py) (`AC`, `CD`, `STRIP`, `ScaleUp`, `IBD-PSC`, `Frequency`, `BaDExpert`, `NC`, `SFT`, `IBAU`)

- See [BTIDBF/](BTIDBF) (`BTI-DBF`)


## TODO before You Start

- Datasets:
  * Original CIFAR10 and GTSRB datasets would be automatically downloaded. 
  * ImageNet should be manually downloaded from [Kaggle](https://www.kaggle.com/competitions/imagenet-object-localization-challenge/data) or other available sources. Then set up the local path to your ImageNet dataset via the `imagenet_dir` variable in [config.py](config.py).
- Before any experiments, first initialize the clean reserved data and validation data using command `python create_clean_set.py -dataset=$DATASET -clean_budget $N`, where `$DATASET = cifar10, gtsrb, ember, imagenet`, `$N = 2000` for `cifar10, gtsrb`, `$N = 5000` for `imagenet`.
- Before launching `clean_label` attack, run [data/cifar10/clean_label/setup.sh](data/cifar10/clean_label/setup.sh).
- Before launching `dynamic` attack, download pretrained generators `all2one_cifar10_ckpt.pth.tar` and `all2one_gtsrb_ckpt.pth.tar` to [models/](models/) from https://drive.google.com/file/d/1vG44QYPkJjlOvPs7GpCL2MU8iJfOi0ei/view?usp=sharing and https://drive.google.com/file/d/1x01TDPwvSyMlCMDFd8nG05bHeh1jlSyx/view?usp=sharing.
- `SPECTRE` baseline defense is implemented in Julia. To compare our defense with `SPECTRE`, you must install Julia and install dependencies before running SPECTRE, see [cleansers_tool_box/spectre/README.md](cleansers_tool_box/spectre/README.md) for configuration details.
- `Frequency` baseline defense is based on Tensorflow. If you would like to reproduce their results, please install Tensorflow (code is tested with Tensorflow 2.8.1 and should be compatible with newer versions) manually, after installing all the dependencies upon. We suggest you create and use a separate (conda) environment for it.


## Quick Start

For example, to launch and defend against the Adaptive-Blend attack:
```bash
# Create a poisoned training set
python create_poisoned_set.py -dataset=cifar10 -poison_type=adaptive_blend -poison_rate=0.003 -cover_rate=0.003 -alpha 0.15

# Train on the poisoned training set
python train_on_poisoned_set.py -dataset=cifar10 -poison_type=adaptive_blend -poison_rate=0.003 -cover_rate=0.003 -alpha 0.15 -test_alpha 0.2

# Test the backdoor model
python test_model.py -dataset=cifar10 -poison_type=adaptive_blend -poison_rate=0.003 -cover_rate=0.003 -alpha 0.15 -test_alpha 0.2

# Visualize
## $METHOD = ['pca', 'tsne', 'oracle']
python visualize.py -method=$METHOD -dataset=cifar10 -poison_type=adaptive_blend -poison_rate=0.003 -cover_rate=0.003 -alpha 0.15 -test_alpha 0.2

# Cleanse with other cleansers
## Except for 'Frequency', you need to train poisoned backdoor models first.
## $CLEANSER = ['SCAn', 'AC', 'SS', 'Strip', 'SPECTRE', 'SentiNet', 'Frequency', etc.]
python cleanser.py -cleanser=$CLEANSER -dataset=cifar10 -poison_type=adaptive_blend -poison_rate=0.003 -cover_rate=0.003 -alpha 0.15 -test_alpha 0.2

# Retrain on cleansed set
## $CLEANSER = ['SCAn', 'AC', 'SS', 'Strip', 'SPECTRE', 'SentiNet', etc.]
python train_on_cleansed_set.py -cleanser=$CLEANSER -dataset=cifar10 -poison_type=adaptive_blend -poison_rate=0.003 -cover_rate=0.003 -alpha 0.15 -test_alpha 0.2

# Other defenses
## $DEFENSE = ['ABL', 'NC', 'NAD', 'STRIP', 'FP', 'SentiNet', 'IBD_PSC', etc.]
## Except for 'ABL', you need to train poisoned backdoor models first.
python other_defense.py -defense=$DEFENSE -dataset=cifar10 -poison_type=adaptive_blend -poison_rate=0.003 -cover_rate=0.003 -alpha 0.15 -test_alpha 0.2
```

<!-- **Notice**:
- `SPECTRE` is implemented in Julia. So you must install Julia and install dependencies before running SPECTRE, see [cleansers_tool_box/spectre/README.md](cleansers_tool_box/spectre/README.md) for configuration details.
- For `clean_label` attack, run [data/cifar10/clean_label/setup.sh](data/cifar10/clean_label/setup.sh) before the first time launching it.
- For `dynamic` attack, download pretrained generators `all2one_cifar10_ckpt.pth.tar` and `all2one_gtsrb_ckpt.pth.tar` to `[models/](models/) from https://drive.google.com/file/d/1vG44QYPkJjlOvPs7GpCL2MU8iJfOi0ei/view?usp=sharing and https://drive.google.com/file/d/1x01TDPwvSyMlCMDFd8nG05bHeh1jlSyx/view?usp=sharing before the first time launching it. -->
 <!-- https://github.com/VinAIResearch/input-aware-backdoor-attack-release before the first time launching it. -->

Some examples for creating other backdoor poison datasets:
```bash
# CIFAR10
python create_poisoned_set.py -dataset cifar10 -poison_type none
python create_poisoned_set.py -dataset cifar10 -poison_type badnet -poison_rate 0.003
python create_poisoned_set.py -dataset cifar10 -poison_type blend -poison_rate 0.003
python create_poisoned_set.py -dataset cifar10 -poison_type trojan -poison_rate 0.003
python create_poisoned_set.py -dataset cifar10 -poison_type clean_label -poison_rate 0.003
python create_poisoned_set.py -dataset cifar10 -poison_type SIG -poison_rate 0.02
python create_poisoned_set.py -dataset cifar10 -poison_type dynamic -poison_rate 0.003
python create_poisoned_set.py -dataset cifar10 -poison_type ISSBA -poison_rate 0.02
python create_poisoned_set.py -dataset cifar10 -poison_type WaNet -poison_rate 0.05 -cover_rate 0.1
python create_poisoned_set.py -dataset cifar10 -poison_type TaCT -poison_rate 0.003 -cover_rate 0.003
python create_poisoned_set.py -dataset cifar10 -poison_type adaptive_blend -poison_rate 0.003 -cover_rate 0.003 -alpha 0.15
python create_poisoned_set.py -dataset cifar10 -poison_type adaptive_patch -poison_rate 0.003 -cover_rate 0.006
```
