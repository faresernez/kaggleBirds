Hello, this my code for the BirdC* competition. /n
More or less clean code is in kaggleBirds/code , the other files were written in a rush for the 2023 competition, they're of lower quality and importance./n
The competition goal is to predict the singing birds (182 different species) in each 5s-frame in approximately 1100 audio of 5mn./n
The data comes from independent bird lovers that upload their recordings in xenocanto.org. We have 7gb of labeled soundscapes and 14gb of unlabeled soundscapes, more unlabeled soundscapes can be found on internet though./n
The data can be noisy and is very unbalanced (700mb for the most recorded bird and 0.5mb for the lowest recorded one). /n
The labeling is of poor quality because birds don't necessarily sing during the whole record and it is done by amateur bird lovers./n
There is a big computing constraint in this challenge. The model needs run on the kaggle gpu or cpu for 2 hours maximum. A lightweight model will be primordial.

The overall idea is to train an autoencoder on the unlabeled soundscapes and use the encoder plus some other convolutional and linear layers to train the classifier./n
I will be training 30 models: 2 groups of 15 models capable of predictiong 12 birds. This means that every bird can be predicted by exactly 2 models. /n
I will use conformal prediction to predict a set of birds with each model and decide that a bird is singing only if it is present in both prediction sets./n
If I am not wrong, this is a novel use of conformal prediction, I hope it turns out to be effective./n

Implementing a costum data loader for the unlabeled data was essential because I couldn't fit the loaded arrays of 7gb of data in my disk. /n
Plus it's more costly to fetch every batch from the disk than to fetch the audio and do the processing./n

I am using a modified version of ULite, a lightweight U-Net-based autoencoder I found on github (https://arxiv.org/abs/2306.16103). /n
It seems to work better than the one I coded myself.
UNet was first designed especially for medical image segmentation. It showed such good results that it used in many other fields after. /n
Since the task of predictiong multiple birds singing can be brought to the segmentation of a spectrogram, I figured that this architecture could help. /n

What is done so far:
  - data exploration
  - implemention of the data loaders, the processor (mel-spectrograms), the autoencoder and the finetuner, the training scripts
  - training of the autoenoder (MSE loss = 4.9013e-09) 
  - I was able to achieve 70% accuracy with the 12 most recorded birds and 50% accuracy with the least recorded birds when fituning the encoder + other layers (cnn + linear)
  - I tried to freeze the encoder in the classifier but it doesn't ssem to work, I will probably re-try it using optuna to finetune the hyperparameters. It would allow me to save some precious computing time: same encoder and different additional layers for every classifier

What will be done next:
  - Train the 30 models with optuna to fintune the hyperparameters of each classifier
  - Implement the conformal prediction code
  - Implement the inference code
  - make a first submission

I need to make a first submission as soon as possible, I will look for ways to improve my model after that, I think of:
  - Data augmentation (adding noise, mixing two records, etc..)
  - Preprocessing techniques (denoising, birds singing detection, etc..)
  - Other processing techniques (mfccs, etc..)
  - finetune the hyperparameters of the encoder and train on more data
  - Cross validation
  - Add 'nothing' class in each subgroup to learn to predict when there is no singing bird in the time frame

What need to be done but is not urgent:
  - Adding a logger in Experience class
  - Cleaning the code, classes are not well written
  - Keeping track of the experiments (I am using a .txt file for now)
