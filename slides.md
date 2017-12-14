class: center, middle

# <small>(re)</small>Introducing Guild AI

### TensorFlow Chicago - Dec 13, 2017

---

# Demo
--

- Installing Guild AI
--

- Finding a package
--

- Installing a package
--

- Training a model
--

- View training progress in TensorBoard

---

# Packages
--

- Encapsulate model definitions and other package files (scripts, resources, etc.)
--

- Defined in a package file <small>(YAML format named `PACKAGE`)</small>
--

- Express dependencies on Guild packages or Python packages from PyPI
--

- Package artifact pip wheel format

---

# magenta/melody-rnn/PACKAGE

``` yaml
name: magenta.melody-rnn
version: 0.1.0.dev1
description: Melody generation using an LSTM from the Magenta project
maintainer: Guild AI
maintainer-email: packages@guild.ai
requires: [pypi.magenta]
license: Apache 2.0
tags:
  - magenta
  - google
  - brain
  - music
  - midi

```

---

# mnist/PACKAGE

``` yaml
name: mnist
version: 0.1.3.dev1
description: CNN and softmax regression classifiers for MNIST digits
maintainer: Guild AI
maintainer-email: packages@guild.ai
license: Apache 2.0
tags: [mnist, cnn, softmax, images, model]
resources:
  dataset:
    description: Yann Lecuns MNIST dataset in compressed IDX format
    path: mnist-idx-data
    sources:
    - url: http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz
      sha256: 440fcabf73cc546fa21475e81ea370265605f56be210a4024d2ca8f203523609
    - url: http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz
      sha256: 3552534a0a558bbed6aed32b30c495cca23d567ec52cac8be1a0730e8010255c
    - url: http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz
      sha256: 8d422c7b0a1c1c79245a5bcf07fe86e33eeafee792b84584aec276f5a2dbc4e6
    - url: http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz
      sha256: f7ae60f92e00ec6debd23a6088c31dbd2371eca3ffa0defaefb259924204aec6
```

---

# Package commands
--

- Find a package - `guild search`
--

- Install a package - `guild install`
--

- List packages - `guild packages`
--

- Uninstall a package - `guild uninstall`
--

- Register a packge - `guild package --upload`

---

# Models

- Defined in a model file<br><small>(YAML format named `MODEL` or `MODELS`)</small>
--

- Define operations and resources
--

- Support for reuse via includes and inheritance

---

class: small-code

# mnist/MODELS


```
- name: mnist-base
  private: yes
  operations:
    train:
      description: 'Train the {{model_desc}}'
      cmd: '{{train_cmd}} --datadir mnist-idx-data --rundir .'
      requires: mnist/dataset
      flags:
        batch-size:
          description: Number of images to include in a training batch
          default: 100
        epochs:
          description: Number of epochs to train
          default: 10

- name: mnist-softmax
  description: Softmax regression classifier for MNIST
  extends: mnist-base
  params:
    model_desc: softmax regression
    train_cmd: softmax

- name: mnist-cnn
  description: CNN classifier for MNIST
  extends: mnist-base
  params:
    model_desc: CNN
    train_cmd: cnn

```

---

# Model commands

- List models - `guild models`
--

- List operations - `guild operations`
--

- Help for a model - `guild help`

---

# Operations
--

- Define commands associated with a model (e.g. *prepare*, *train*,
  *evaluate*, *predict*, *generate*)
--

- Invoke command line operations (command arguments, environment,
  working directory)
--

- Define a model command interface

---

# Flags

- Named values passed as options to operation commands
- Defined at the model and operation level
- Provide an interface to model operations

---

# Operation help

```bash
$ guild run resnet-50:train --help-op
Usage: guild run [OPTIONS] slim-resnet-50:train [FLAG]...

Train ResNet-50

Use 'guild run --help' for a list of options.

Flags:
  batch-size                Number of samples in each batch (default is 32)
  dataset                   Dataset to train with (cifar10, mnist, flowers,
                            custom) (required)
  learning-rate             Initial learning rate (default is 0.01)
  learning-rate-decay-type  How the learning rate is decayed (default is
                            'exponential')
  log-every-n-steps         Steps between status updates (default is 100)
  max-steps                 Maximum number of training steps (default is 1000)
  optimizer                 Training optimizer (adadelta, adagrad, adam, ftrl,
                            momentum, sgd, rmsprop) (default is 'rmsprop')
  save-model-secs           Seconds between model saves (default is 60)
  save-summaries-secs       Seconds between summary saves (default is 60)
  weight-decay              Weight decay on the model weights (default is
                            4e-05)
```

---

# Runs
--

- Operation system process associated with a model operation
--

- Associated with a *run directory*
--

- Artifacts (run metadata, resolved dependencies, and output) stored
  within run directory
--

- Full management interface

---

# Sample run info with flags

```bash
$ guild runs info 703f3c06 --flags
id: 703f3c06d85b11e7bfc6107b44920855
operation: ./slim-resnet:fine-tune
status: completed
started: 2017-12-03 12:54:49
stopped: 2017-12-03 12:57:25
rundir: /home/garrett/.guild/runs/703f3c06d85b11e7bfc6107b44920855
command: /usr/bin/python2 -u /home/garrett/.local/lib/python2.7/site-packages...
exit_status: 0
pid:
label: 0.93 acc (fine-tune w/1000 steps)
flags:
batch-size: 32
dataset: flowers
layers: 50
learning-rate: 0.01
learning-rate-decay-type: exponential
log-every-n-steps: 100
max-steps: 1000
optimizer: rmsprop
save-model-secs: 60
save-summaries-secs: 60
weight-decay: 4e-05
```

---

# Run commands
--

- Run an operation - `guild run`
--

- Show run information - `guild runs info`
--

- List runs - `guild runs`
--

- Label a run - `guild runs label`
--

- Delete runs - `guild runs rm`

---

# Resources
--

- Files used by operations
--

- Resource sources - URL, model local file, operation output
--

- Used as operation dependency
--

- Resolved for each run via symlinks
--

- Cached for reuse across runs

---

# Other features
--

- Compare runs - `guild compare`
--

- View TensorFlow events with TensorBoard - `guild tensorboard`
--

- Extensible plugin API (e.g. used to support Keras, CloudML,
  TensorFlow Serving, etc.)
--

- Remote runs with sync - `guild sync`

---

# Roadmap
--

- v0.3.0 in Jan 2018
--

- v0.3.1 in Q2 2018
--

- v0.4.0 in Q3 2018

---

# Roadmap <small>(cont)</small>
--

- More packages!
--

- Windows support
--

- Run diffing
--

- Guild View
--

- Developer API
--

- More runtime environment support (AWS, Serving, CloudML)

---

class: center, middle

# Wrapping up

---

# Links

- Website - https://guild.ai
- GitHub - https://github.com/guildai

  /guild - Guild CLI

  /packages - Package definitions

  /examples - Model examples
