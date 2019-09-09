<h1 align='center'>
    Flight rules for machine learning
</h1>

<h4 align='center'>
    A guide for astronauts (now, people doing machine learning) about what to do when things go wrong.
</h4>

<p align='center'>
    <a href="https://forthebadge.com">
        <img src="https://forthebadge.com/images/badges/built-with-love.svg" alt="forthebadge">
    </a>
    <a href="https://forthebadge.com">
        <img src="https://forthebadge.com/images/badges/cc-sa.svg" alt="forthebadge">
    </a>
    <a href="https://github.com/prettier/prettier">
        <img src="https://img.shields.io/badge/code_style-prettier-ff69b4.svg?style=flat-square" alt="code style: prettier" />
    </a>
    <a href="http://makeapullrequest.com">
        <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square" alt="PRs Welcome">
    </a>
    <a href="https://github.com/bkkaggle/machine-learning-flight-rules/pulls">
        <img alt="GitHub pull requests" src="https://img.shields.io/github/issues-pr/bkkaggle/machine-learning-flight-rules">
    </a>
</p>

<p align='center'>
    <a href='#what-are-flight-rules'>What are flight rules?</a> •
    <a href='#contributing'>Contributing</a> •
    <a href='#authors'>Authors</a> •
    <a href='#license'>License</a> •
    <a href='#acknowledgements'>Acknowledgements</a>
</p>

# What are "flight rules"?

_Copied from: https://github.com/k88hudson/git-flight-rules_

> _Flight Rules_ are the hard-earned body of knowledge recorded in manuals that list, step-by-step, what to do if X occurs, and why. Essentially, they are extremely detailed, scenario-specific standard operating procedures. [...]

> NASA has been capturing our missteps, disasters and solutions since the early 1960s, when Mercury-era ground teams first started gathering "lessons learned" into a compendium that now lists thousands of problematic situations, from engine failure to busted hatch handles to computer glitches, and their solutions.

&mdash; Chris Hadfield, _An Astronaut's Guide to Life_.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

-   [General tips](#general-tips)
    -   [Look at the wrongly classified predictions of your network](#look-at-the-wrongly-classified-predictions-of-your-network)
    -   [Always set the random seed](#always-set-the-random-seed)
    -   [Make a baseline and then increase the size of your model until it overfits](#make-a-baseline-and-then-increase-the-size-of-your-model-until-it-overfits)
        -   [Use a very simplified baseline to test that your code works correctly](#use-a-very-simplified-baseline-to-test-that-your-code-works-correctly)
        -   [Overfit on a single batch](#overfit-on-a-single-batch)
        -   [Be sure that you're data has been correctly processed](#be-sure-that-youre-data-has-been-correctly-processed)
        -   [Simple models -> complex models](#simple-models---complex-models)
        -   [Start with a simple optimizer](#start-with-a-simple-optimizer)
        -   [Change one thing at a time](#change-one-thing-at-a-time)
    -   [Regularize your model](#regularize-your-model)
        -   [Get more data](#get-more-data)
        -   [Data augmentation](#data-augmentation)
        -   [Use a pretrained network](#use-a-pretrained-network)
        -   [Decrease the batch size](#decrease-the-batch-size)
        -   [Use early stopping](#use-early-stopping)
    -   [Squeeze out more performance out of the network](#squeeze-out-more-performance-out-of-the-network)
        -   [Ensemble](#ensemble)
        -   [Use early stopping on the val metric](#use-early-stopping-on-the-val-metric)
    -   [Learn to deal with long iteration times](#learn-to-deal-with-long-iteration-times)
    -   [Keep a log of what you're working on](#keep-a-log-of-what-youre-working-on)
    -   [Try to predict how your code will fail](#try-to-predict-how-your-code-will-fail)
    -   [Resources](#resources)
-   [Advanced tips](#advanced-tips)
    -   [Mixed/half precision training](#mixedhalf-precision-training)
    -   [multi gpu/machine training](#multi-gpumachine-training)
    -   [determinism](#determinism)
    -   [initalization](#initalization)
        -   [Initialization methodology](#initialization-methodology)
        -   [Types of intialization](#types-of-intialization)
            -   [Xavier or glorot initialization](#xavier-or-glorot-initialization)
            -   [Kaiming or he initialization](#kaiming-or-he-initialization)
        -   [Gain](#gain)
        -   [Pytorch defaults](#pytorch-defaults)
        -   [Resources](#resources-1)
    -   [Normalization](#normalization)
        -   [Batch norm](#batch-norm)
            -   [You can't use a batch size of 1 with batch norm](#you-cant-use-a-batch-size-of-1-with-batch-norm)
            -   [Be sure to use model.eval() with batch norm](#be-sure-to-use-modeleval-with-batch-norm)
            -   [Resources](#resources-2)
-   [Common errors](#common-errors)
-   [Pytorch](#pytorch)
    -   [Tensorboard](#tensorboard)
    -   [Common errors](#common-errors-1)
    -   [How to](#how-to)
    -   [Text](#text)
-   [Kaggle](#kaggle)
    -   [ensembling](#ensembling)
    -   [Tensorboard in kernels](#tensorboard-in-kernels)
-   [Computer vision](#computer-vision)
-   [Semantic segmentation](#semantic-segmentation)
-   [NLP](#nlp)
-   [Gradient boosting](#gradient-boosting)
-   [setup](#setup)
-   [Build your own library](#build-your-own-library)
-   [Resources](#resources-3)
    -   [model zoos](#model-zoos)
    -   [Arxiv alternatives](#arxiv-alternatives)
    -   [Demos](#demos)
    -   [Discover](#discover)
    -   [Machine learning as a service](#machine-learning-as-a-service)
    -   [Coreml](#coreml)
    -   [Courses](#courses)
    -   [Miscelaneous](#miscelaneous)
-   [ideas](#ideas)
-   [Contributing](#contributing)
-   [Authors](#authors)
-   [License](#license)
-   [Acknowledgements](#acknowledgements)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## General tips

https://karpathy.github.io/2019/04/25/recipe has some great best practices for training neural networks. Some of these tips include:

### Look at the wrongly classified predictions of your network

This can help tell you what might be wrong with your dataset or model.

### Always set the random seed

This will prevent (most, but not all!) variation in results between otherwise identical training runs.

If you're using pytorch, you can set the random seed by following: _link to random seed setting pytorch_

### Make a baseline and then increase the size of your model until it overfits

#### Use a very simplified baseline to test that your code works correctly

Use a simple model (e.g. a small resnet18 or linear regression) and confirm that your code works properly and as it is supposed to.

#### Overfit on a single batch

Try using as small of a batch size as you can (if you're using batch normalization, that would be a batch of two examples). Your loss should go down to zero within a few iterations. If it doesn't, that means you have a problem somewhere in your code.

#### Be sure that you're data has been correctly processed

Visualize your input data right before the `out = model(x)` to be sure that the data being sent to the network is correct (data has normalized properly, augmentations have been applied correctly, etc)

#### Simple models -> complex models

In most cases, start with a simple model (eg: resnet18) then go on to using larger and more complex models (eg: SE-ResNeXt-101).

#### Start with a simple optimizer

Adam is almost always a safe choice, It works well and doesn't need extensive hyperparameter tuning. Kaparthy suggests using it with a learning rate of 3e-4.
I usually start with SGD with a learning rate of 0.1 and a momentum of 0.9 for most image classification and segmentation tasks.

#### Change one thing at a time

Change one hyperparameter/augmentation/architecture and see its effects on the performance of your network. Changing multiple things at a time won't tell you what changes helped and which didn't.

### Regularize your model

#### Get more data

Training on more data will always decrease the amount of overfitting and is the easiest way to regularize a model

#### Data augmentation

This will artificially increase the size of your dataset and is the next best thing to collecting more data. Be sure that the augmentations you use make sense in the context of the task (flipping images of text in an OCR task left to right will hurt your model instead of helping it).

#### Use a pretrained network

Pretrained networks (usually on Imagenet) help jumpstart your model especially when you have a smaller dataset. The domain of the pretrained network doesn't usually prevent it from helping although pretraining on a similar domain will be better.

#### Decrease the batch size

Smaller batch sizes usually help increase regularization

#### Use early stopping

Use the validation loss to only save the best performing checkpoint of the network after the val loss hasn't gone down for a certain number of epochs

### Squeeze out more performance out of the network

#### Ensemble

Ensemble multiple models either trained on different cross validation splits of the dataset or using different architectures. This always boosts performance by a few percentage points and gives you a more confident measure of the performance of the model on the dataset. Averaging metrics from models in an ensemble will help you figure out whether a change in the model is actually an improvement or random noise.

#### Use early stopping on the val metric

-   Increase the size of the model until you overfit, then add regularization
-   augmentation on mask
-   correlation in ensembles
-   noise in ensembling

Another great resource for best practices when training neural networks is (http://amid.fish/reproducing-deep-rl). This article focused on best practices for deep rl, but most of its recommendations are still useful on normal machine learning. Some of these tips include:

### Learn to deal with long iteration times

Most normal programming (web development, IOS development, etc) iteration times usually range in the seconds, but iteration times in machine learning range from minutes to hours. This means that "experimenting a lot and thinking a little", which is usually fine in other programming contexts, will make you waste a lot of time waiting for a training run to finish. Instead, spending more time thinking about what your code does and how it might not work will help you make less mistakes and waste less time.

### Keep a log of what you're working on

Keeping records (tensorboard graphs/model checkpoints/metrics) of training runs and configurations will really help you out when figuring out what worked and what didn't. Additionally, keeping track of what you're working on and your mindset as you're working through a problem will help you when you have to come back to your work days or weeks later.

### Try to predict how your code will fail

Doing this will cut down on the amount of failures that seem obvious in retrospect. I've sometimes had problems where I knew what was wrong with my code before going through the code to debug it. To stop making as many obvious mistakes, I wouldn't start a new training run if I was uncertain about whether it would work, and then would find and fix what might have gone wrong.

### Resources

-   https://karpathy.github.io/2019/04/25/recipe
-   http://amid.fish/reproducing-deep-rl

## Advanced tips

-   from: https://gist.github.com/bkkaggle/67bb9b5e6132e5d3c30e366c8d403369
-   Try more architectures
-   Try using checkpoint ensembling
-   Basic architectures are sometimes better
-   Use distribution of oov embeddings
-   Try other forms of ensembling than cv
-   Weighted average of embeddings
-   Blend with linear regression
-   Spatial dropout after embeddings
-   Rely more on shakeup predictions
-   Gaussian noise after embeddings
-   Make sure copied code is correct
-   Use convolutions on the outputs on rnns
-   Pay more attention to correlations between folds
-   Use batch norm after dense layers
-   Try not to extensively tune hyperparameters
-   Try one-cycle
-   Optimizing thresholds can lead to "brittle" models
-   Use more of spacy's features to get less oov words
-   Random initializations between folds might help diversity
-   Look at pos tagging
-   Something that works for someone might not help you
-   Look into label smoothing?
-   reinit embedding matrix between runs
-   Use multiprocessing
-   Train model and throw away very confidant predictions
-   Consider using bce + soft f1 loss
-   Bucket sentences in batches with similar lengths
-   Mask before softmax
-   What boosted my model most was unfreezing embeddings towards the end of each run and updating unknown words so that subsequent models/ folds could use more words for training. (https://www.kaggle.com/c/quora-insincere-questions-classification/discussion/80542)
-   Look at hill climbing to get best coefs
-   Make sure cyclic lr ends training with the lr at lowest point
-   Concentrate on embeddings layer and get least amount of oov words
-   If you have continous features, bin them and add as categorical auxillary features/targets\
-   vocabulary on train val and test between folds can lead to information being leaked and artificially increases cv score
-   Try using checkpoint ensembling
-   Use distribution of oov embeddings
-   Weighted average of embeddings
-   Spatial dropout after embeddings
-   Gaussian noise after embeddings
-   Use convolutions on the outputs on rnns
-   Use batch norm after dense layers
-   Try one-cycle
-   Use more of spacy's features to get less oov words\
-   Look at pos tagging
-   https://www.kaggle.com/ryches/22nd-place-solution-6-models-pos-tagging
-   https://www.kaggle.com/ryches/parts-of-speech-disambiguation-error-analysis \
-   reinit embedding matrix between runs
-   optimize for the metric
-   can bias different models towards different subgroups
-   drop 50% of negative samples (concentrate only on important samples)
-   use batch random samplers, gradient accumulation, mixed precision
-   if you don't want to use loss weighting, remove negative samples from the dataset (https://www.kaggle.com/c/jigsaw-unintended-bias-in-toxicity-classification/discussion/ 97484)
-   sort data by length in torch dataset and don't use a random sampler
-   manually init layers
-   Look at hill climbing to get best coefs
-   Make sure cyclic lr ends training with the lr at lowest point
-   Concentrate on embeddings layer and get least amount of oov words

### Mixed/half precision training

Mixed or half precision training lets you train on larger batch sizes and can speed up your training.

#### What is the difference between mixed and half precision training?

Nvidia's Volta and Turing GPUs contain tensor cores that can do fast fp16 matrix multiplications and significantly speed up your training.

"True" half precision training casts the inputs and the model's parameters to 16 bit floats and computes everything using 16 bit floats. The advantages of this are that fp16 floats only use half the amount of vram as normal fp32 floats, letting you double the batch size while training. This is the fastest and most optimized way to take advantage of tensor cores, but comes at a cost. Using fp16 floats for the model's parameters and batch norm statistics means that if the gradients are small enough, they can underflow and be replaced by zeros.

Mixed precision solves these problems at a small overhead by keeping a master copy of the model's parameters in 32 bit floats. The inputs and the model's parameters are still cast to fp16, but after the backwards pass, the gradients are copied to the master copy and cast to fp32. The parameters are updated in fp32 to prevent gradients from underflowing, and the new, updated master copy's parameters are cast to fp16 and copied to the original fp16 model. Nvidia's apex library recommends using mixed precision in a different way by casting inputs to tensor core-friendly operations to fp16 and keeping other operations in fp32. Both of these mixed precision approaches have an overhead compared to half precision training, but are faster and use less vram than fp32 training.

_example_

#### Resources

If you're using pytorch, Nvidia's apex library (https://docs.nvidia.com/deeplearning/sdk/mixed-precision-training/index.html) is the easiest way to start using mixed precision.
If you want to read more about half and mixed precision training, take a look at https://forums.fast.ai/t/mixed-precision-training/20720

### gradient accumulation

### checkpointing

### multi gpu/machine training

-   https://medium.com/huggingface/training-larger-batches-practical-tips-on-1-gpu-multi-gpu-distributed-setups-ec88c3e51255#

### determinism

-   https://discuss.pytorch.org/t/how-to-get-deterministic-behavior/18177/7

### initalization

_edit_

#### Initialization methodology

-   means and stddevs of activations should be close to 0 and 1 to prevent gradients exploding or vanishing
-   activations of layers have stddevs close to sqrt(num_input_channels)
-   so, to get the stddevs back to 1, multiply random weights by 1 / sqrt(c_in)
-   bias weights should be initialized to 0
-   this works well without activations, but results in vanishing or exploding gradients when used with a tanh or sigmoid activation function

-   intializations can either be from a uniform distribution or a normal distribution
-   use xavier for sigmoid and softmax activations
-   use kaiming for relu or leaky relu activations

#### Types of intialization

##### Xavier or glorot initialization

-   uniform initialization
    -   bound a uniform distribution between +/- sqrt(6 / (c_in + c_out))
-   normal initialization

    -   multiply a normal distribution by sqrt(2 / (c_in + c_out))
    -   or create a normal distribution with mean 0 and stddev sqrt(2 / (c_in + c_out))

-   helps keep identical variances across layers

##### Kaiming or he initialization

-   when using a relu activation, stddevs will be close to sqrt(c_in)/sqrt(2), so multiplying the normally distributed activations by sqrt(2/c_in) will make the activations have a stddev close to 1

-   uniform initialization
    -   bound a uniform distribution between +/- sqrt(6 / c_in)
-   normal initialization
    -   multiply a normal distribution by sqrt(2 / c_in)
    -   or create a normal distribution with mean 0 and stddev sqrt(2 / c_in)

#### Gain

-   multiplied to init bounds/stddevs
-   sqrt(2) for relu
-   none for kaiming

#### Pytorch defaults

-   most layers are initialized with kaiming uniform as a reasonable default
-   use kaiming with correct gain (https://pytorch.org/docs/stable/nn.html#torch.nn.init.calculate_gain)

#### Resources

-   https://gist.github.com/bkkaggle/58d4e58ac2a5101e42e2d1af9399c638
-   https://github.com/pytorch/pytorch/issues/15314
-   https://medium.com/@sakeshpusuluri123/activation-functions-and-weight-initialization-in-deep-learning-ebc326e62a5c
-   https://pytorch.org/docs/stable/_modules/torch/nn/init.html
-   https://discuss.pytorch.org/t/whats-the-default-initialization-methods-for-layers/3157/21
-   https://towardsdatascience.com/weight-initialization-in-neural-networks-a-journey-from-the-basics-to-kaiming-954fb9b47c79
-   https://towardsdatascience.com/hyper-parameters-in-action-part-ii-weight-initializers-35aee1a28404
-   https://pytorch.org/docs/stable/nn.html#torch.nn.init.calculate_gain
-   https://github.com/mratsim/Arraymancer/blob/master/src/nn/init.nim
-   https://jamesmccaffrey.wordpress.com/2018/08/21/pytorch-neural-network-weights-and-biases-initialization/

### Normalization

#### Batch norm

The original batch normalization paper put the batch norm layer before the activation function, recent research shows that putting the batch norm layer after the activation gives better results. A great article on batch norm and why it works can be found here (https://blog.paperspace.com/busting-the-myths-about-batch-normalization/).

##### You can't use a batch size of 1 with batch norm

Batch norm relies on the mean and variance of all the elements in a batch, it won't work if you're using a batch size of one while training, so either skip over any leftover batches with batch sizes of 1 or increase the batch size to atleast 2.

_Link to batch size 1 error_

##### Be sure to use model.eval() with batch norm

Run `model.eval()` before your validation loop to make sure pytorch uses the running mean and variance calculated over the training set. Also make sure to call `model.train()` before your training loop to start calculating the batch norm statistics again. You can read more about this at (https://discuss.pytorch.org/t/what-does-model-eval-do-for-batchnorm-layer/7146)

_link to pytorch model.eval() tip_

##### Resources

http://mlexplained.com/2018/11/30/an-overview-of-normalization-methods-in-deep-learning/ is a really good blog post on the different types of normalizations and when to them.

## Common errors

-   https://github.com/NVIDIA/apex/issues/259

## Pytorch

-   https://discuss.pytorch.org/t/logsoftmax-vs-softmax/21386/9
-   https://discuss.pytorch.org/t/about-bidirectional-gru-with-seq2seq-example-and-some-modifications/15588/5
-   https://discuss.pytorch.org/t/when-to-set-pin-memory-to-true/19723
-   https://discuss.pytorch.org/t/model-eval-vs-with-torch-no-grad/19615/11
-   https://discuss.pytorch.org/t/guidelines-for-assigning-num-workers-to-dataloader/813

-   https://github.com/HarisIqbal88/PlotNeuralNet
-   https://stanford.edu/~shervine/blog/pytorch-how-to-generate-data-parallel

### Tensorboard

-   https://discuss.pytorch.org/t/check-gradient-flow-in-network/15063/8
-   https://stackoverflow.com/questions/48816873/intermediate-layer-makes-tensorflow-optimizer-to-stop-working
-   https://github.com/lanpa/tensorboardX/issues/345
-   https://stackoverflow.com/questions/42315202/understanding-tensorboard-weight-histograms
-   https://discuss.pytorch.org/t/tensorboard-in-pytorch-1-1/44515/2
-   https://discuss.pytorch.org/t/how-to-print-models-parameters-with-its-name-and-requires-grad-value/10778/3
-   https://www.tensorflow.org/guide/tensorboard_histograms
-   https://stackoverflow.com/questions/38149622/what-is-a-good-explanation-of-how-to-read-the-histogram-feature-of-tensorboard
-   https://stats.stackexchange.com/questions/220491/how-does-one-interpret-histograms-given-by-tensorflow-in-tensorboard

### Common errors

-   https://discuss.pytorch.org/t/unable-to-write-to-file-torch-18692-1954506624/9990
-   https://discuss.pytorch.org/t/torch-from-numpy-not-support-negative-strides/3663
-   https://discuss.pytorch.org/t/encounter-the-runtimeerror-one-of-the-variables-needed-for-gradient-computation-has-been-modified-by-an-inplace-operation/836
-   https://discuss.pytorch.org/t/solved-creating-mtgp-constants-failed-error/15084
-   https://github.com/pytorch/pytorch/issues/4534

### How to

-   https://discuss.pytorch.org/t/how-can-i-replace-an-intermediate-layer-in-a-pre-trained-network/3586
-   https://discuss.pytorch.org/t/concatenation-of-the-hidden-states-produced-by-a-bidirectional-lstm/3686
-   https://discuss.pytorch.org/t/global-max-pooling/1345
-   https://discuss.pytorch.org/t/how-can-we-release-gpu-memory-cache/14530
-   https://discuss.pytorch.org/t/proper-way-to-do-gradient-clipping/191
-   https://towardsdatascience.com/transfer-learning-using-pytorch-part-2-9c5b18e15551

### Text

-   https://discuss.pytorch.org/t/aligning-torchtext-vocab-index-to-loaded-embedding-pre-trained-weights/20878
-   http://anie.me/On-Torchtext/
-   http://mlexplained.com/2018/02/15/language-modeling-tutorial-in-torchtext-practical-torchtext-part-2/
-   http://mlexplained.com/2018/02/08/a-comprehensive-tutorial-to-torchtext/
-   https://github.com/pytorch/text/issues/303
-   https://github.com/pytorch/text/issues/140
-   https://towardsdatascience.com/use-torchtext-to-load-nlp-datasets-part-ii-f146c8b9a496
-   https://towardsdatascience.com/use-torchtext-to-load-nlp-datasets-part-i-5da6f1c89d84

## Kaggle

-   https://www.kaggle.com/raddar/towards-de-anonymizing-the-data-some-insights
-   https://www.kaggle.com/c/LANL-Earthquake-Prediction/discussion/80250
-   https://www.kaggle.com/c/microsoft-malware-prediction/discussion/80368
-   https://github.com/guoday/ctrNet-tool
-   https://www.kaggle.com/c/microsoft-malware-prediction/discussion/79045
-   https://www.kaggle.com/c/microsoft-malware-prediction/discussion/76668
-   https://www.kaggle.com/c/microsoft-malware-prediction/discussion/78253
-   https://www.kaggle.com/c/microsoft-malware-prediction/discussion/75246
-   https://www.kaggle.com/c/microsoft-malware-prediction/discussion/75246
-   https://www.kaggle.com/c/microsoft-malware-prediction/discussion/75217
-   https://www.kaggle.com/c/talkingdata-adtracking-fraud-detection/discussion/56497#331685
-   https://www.kaggle.com/c/microsoft-malware-prediction/discussion/75149
-   https://github.com/goldentom42/py_ml_utils
-   https://www.kaggle.com/c/jigsaw-unintended-bias-in-toxicity-classification/discussion/87756#latest-521985
-   https://www.kaggle.com/c/jigsaw-unintended-bias-in-toxicity-classification/discussion/87586#latest-519542
-   https://www.kaggle.com/c/quora-insincere-questions-classification/discussion/72040
-   https://www.kaggle.com/c/quora-insincere-questions-classification/discussion/79720
-   https://www.kaggle.com/c/quora-insincere-questions-classification/discussion/79911
-   https://gist.github.com/ceshine/dddbe932b00c8d281ed61ff82dd77593
-   https://www.kaggle.com/c/tgs-salt-identification-challenge/discussion/66465
-   https://www.kaggle.com/c/tgs-salt-identification-challenge/discussion/66660
-   https://www.kaggle.com/c/tgs-salt-identification-challenge/discussion/67209
-   https://www.kaggle.com/c/tgs-salt-identification-challenge/discussion/67090
-   https://becominghuman.ai/investigating-focal-and-dice-loss-for-the-kaggle-2018-data-science-bowl-65fb9af4f36c
-   https://www.kaggle.com/c/tgs-salt-identification-challenge/discussion/65226
-   https://www.kaggle.com/c/planet-understanding-the-amazon-from-space/discussion/36809
-   https://www.kaggle.com/cpmpml/raddar-magic-explained-a-bit

### ensembling

### Tensorboard in kernels

-   https://medium.com/datadriveninvestor/monitor-progress-of-your-training-remotely-f9404d71b720
-   https://medium.com/@lesswire1/monitor-progress-of-your-training-remotely-708ee9d2f174
-   https://serveo.net

## Computer vision

## Semantic segmentation

-   http://blog.qure.ai/notes/semantic-segmentation-deep-learning-review
-   https://tuatini.me/practical-image-segmentation-with-unet/
-   https://www.jeremyjordan.me/semantic-segmentation/#loss

## NLP

-   pytorch nlp
    -   https://discuss.pytorch.org/t/packedsequence-for-seq2seq-model/3907
    -   https://discuss.pytorch.org/t/solved-multiple-packedsequence-input-ordering/2106/7
-   transformers
    -   https://blog.floydhub.com/the-transformer-in-pytorch/
    -   http://www.wildml.com/2016/01/attention-and-memory-in-deep-learning-and-nlp/
    -   https://jalammar.github.io/illustrated-transformer/
    -
-   https://towardsdatascience.com/word2vec-skip-gram-model-part-1-intuition-78614e4d6e0b
-   https://karpathy.github.io/2015/05/21/rnn-effectiveness/
-   http://ruder.io/a-review-of-the-recent-history-of-nlp/
-   http://ruder.io/multi-task/
-   http://ruder.io/multi-task-learning-nlp/
-   https://medium.com/huggingface/learning-meaning-in-natural-language-processing-the-semantics-mega-thread-9c0332dfe28e
-   https://github.com/sebastianruder/NLP-progress
-   https://nlpprogress.com
-   https://medium.com/huggingface/100-times-faster-natural-language-processing-in-python-ee32033bdced
-   https://github.com/salesforce/awd-lstm-lm
-   https://www.fast.ai/2017/08/25/language-modeling-sota/
-   https://towardsdatascience.com/word2vec-skip-gram-model-part-1-intuition-78614e4d6e0b

## Gradient boosting

-   https://sites.google.com/view/lauraepp/parameters
-   https://xgboost.readthedocs.io/en/latest/tutorials/model.html
-   http://mlexplained.com/2018/01/05/lightgbm-and-xgboost-explained/
-   https://github.com/aksnzhy/xlearn

## setup

-   https://stackoverflow.com/questions/43759610/how-to-add-python-3-6-kernel-alongside-3-5-on-jupyter
-   https://www.rosehosting.com/blog/how-to-install-python-3-6-4-on-debian-9/
-   https://forums.fast.ai/t/jupyter-notebook-keyerror-allow-remote-access/24392
-   https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
-   https://stackoverflow.com/questions/35245401/combining-conda-environment-yml-with-pip-requirements-txt
-   https://stackoverflow.com/questions/42352841/how-to-update-an-existing-conda-environment-with-a-yml-file

## Build your own library

-   https://github.com/bkkaggle/L2
-   https://medium.com/@florian.caesar/how-to-create-a-machine-learning-framework-from-scratch-in-491-steps-93428369a4eb
-   https://github.com/joelgrus/joelnet
-   https://medium.com/@johan.mabille/how-we-wrote-xtensor-1-n-n-dimensional-containers-f79f9f4966a7
-   https://mlfromscratch.com
-   https://eisenjulian.github.io/deep-learning-in-100-lines/
-   http://blog.ezyang.com/2019/05/pytorch-internals/

## Resources

### model zoos

-   https://paperswithcode.com
-   https://modelzoo.co/blog
-   https://modeldepot.io/search

### Arxiv alternatives

-   https://www.arxiv-vanity.com
-   http://www.arxiv-sanity.com
-   https://www.scihive.org

### Demos

-   https://ganbreeder.app
-   https://talktotransformer.com
-   https://transformer.huggingface.co
-   https://www.nvidia.com/en-us/research/ai-playground/
-   https://alantian.net/ganshowcase/
-   https://rowanzellers.com/grover/
-   http://nvidia-research-mingyuliu.com/gaugan/
-   http://nvidia-research-mingyuliu.com/petswap/

### Discover

-   https://www.sciencewiki.com
-   https://git.news/?ref=producthunt

### Machine learning as a service

-   https://runwayml.com
-   https://supervise.ly

### Coreml

-   https://developer.apple.com/machine-learning/models/

### Courses

-   https://fast.ai

### Miscelaneous

    -   https://markus-beuckelmann.de/blog/boosting-numpy-blas.html
    -   https://github.com/Wookai/paper-tips-and-tricks
    -   https://github.com/dennybritz/deeplearning-papernotes

# ideas

-   code from quora and toxic competitions
-   github stars
-   julia
-   batch norm before or after relu
-   logo
-   format links

# Contributing

This repository is still a work in progress, so if you find a bug, think there is something missing, or have any suggestions for new features, feel free to open an issue or a pull request. Feel free to use the library or code from it in your own projects, and if you feel that some code used in this project hasn't been properly accredited, please open an issue.

# Authors

-   _Bilal Khan_ - _initial work_

# License

This project is licensed under the CC-BY-SA-4.0 License - see the [license](LICENSE) file for details

# Acknowledgements

-   _k88hudson_ - _Parts of https://github.com/k88hudson/git-flight-rules were used in this repository_

This repository was inspired by https://github.com/k88hudson/git-flight-rules and copied over parts of it
