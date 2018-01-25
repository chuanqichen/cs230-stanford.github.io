---
layout: post
title:  "Splitting into train, dev and test sets"
description: "Short tutorial detailing the best practices to split your dataset into train, dev and test sets"
excerpt: "Best practices to split your dataset into train, dev and test sets"
author: "Guillaume Genthial, Olivier Moindrot, Surag Nair"
date:   2018-01-24
mathjax: true
published: true
tags: theory
github: https://github.com/cs230-stanford/cs230-starter-code
module: Tutorials
---

<!-- TODOs: -->
<!--     - should we include train-dev here? -->
<!--       no, we should only include it if train and dev don't come from the same distribution -->
<!--     - check if we use train/dev or train / dev -->

Splitting your data into training, dev and test sets can be disastrous if not done correctly.
In this short tutorial, we will explain the best practices when splitting your dataset.

<!-- TODO: add link to part 3 -->
This follows part 3 of the class on "Structuring your Machine Learning Project", and adds code examples to the theoretical content.


<!-- TODO: also have the list of posts here? -->
This tutorial has multiple parts:
#TODO: add here links to different posts
- this post: installation, get started with the code for the projects
- [second post][tf-post]: (TensorFlow) explain the global structure of the code
- [third post][tf-vision]: (Tensorflow - Vision) details for the computer vision example
- [fourth post][tf-nlp]: (Tensorflow - NLP) details for the NER example



---
## Theory: how to split into train / train-dev / dev / test

TODO: summarize content from the class

TODO: insist on having same distribution for train / dev / test if possible, or at least dev / test
- make sure that the code does it correctly
- bad example: `dev = filenames[:100]`, but the first 100 filenames are all of the same label


### Objectives


- the split between train / dev / test should **always be the same** across experiments
  - otherwise, different models are not evaluated in the same conditions
- the **dev** and **test** sets should come from the same distribution
- if **train** and **dev** sets have different distributions, include a **train-dev** set
    - the **train** and **train-dev** sets should come from the same distribution


---
## Split into three directories

The best and most secure way to split the data into these three sets is to have one directory for train, one for dev and one for test.

For instance if you have a dataset of images, you could have a structure like this with 80% in the training set, 10% in the dev set and 10% in the test set.
```
data/
    train/
        img_000.jpg
        ...
        img_799.jpg
    dev/
        img_800.jpg
        ...
        img_899.jpg
    test/
        img_900.jpg
        ...
        img_999.jpg
```

#### Build it in a reproducible way

Often a dataset will come either in one big set that you will split into train, dev and test. Academic dataests often come already with a train/test split (to be able to compare different models on a common test set).

You will therefore have to build yourself the split before beginning your project.

A good practice that is true for every software, but especially in machine learning, is to make every step of your project reproducible.
It should be possible to start the project again from scratch and create the same exact split between train, dev and test sets.

One way to do it is to have a `build.py` file that will be called once at the start of the project and will create the split into train, dev and test.
We also need to make sure that any randomness involved in `build.py` uses a **fixed seed**, so that every call to `python build.py` will result in the same outputs.

>Never do the split manually (by moving files into different folders one by one), because you wouldn't be able to reproduce it.

---
## Split at the beginning of training

If the build process contained in `build.py` is not very long, it is possible to do it every time we train the model.
When loading the data, we can just split it into three sets like this for 80% train, 10% dev and 10% test:
```python
filenames = ['img_000.jpg', 'img_001.jpg', ...]

split_1 = int(0.8 * len(filenames))
split_2 = int(0.9 * len(filenames))
train_filenames = filenames[:split_1]
dev_filenames = filenames[split_1:split_2]
test_filenames = filenames[split_2:]
```

#### Ensure that train, dev and test have the same distribution if possible

Often we have a big dataset and want to split it into train, dev and test set. In most cases, each split will have the same distribution as the others.

Suppose that the first 100 images (`img_000.jpg` to `img_099.jpg`) have label 0, the 100 following label 1, ... and the last 100 images have label 9. Then the above code will make the dev set only have label 8, and the test set only label 9.

We therefore need to ensure that the filenames are correctly shuffled before splitting the data.
```python
filenames = ['img_000.jpg', 'img_001.jpg', ...]
random.shuffle(filenames)  # shuffles in place

split_1 = int(0.8 * len(filenames))
split_2 = int(0.9 * len(filenames))
train_filenames = filenames[:split_1]
dev_filenames = filenames[split_1:split_2]
test_filenames = filenames[split_2:]
```


#### Make it reproducible

Here again we need to make sure that the train/dev/test split stays the same across every experiment, so we have to make it reproducible.

The code above doesn't ensure reproducibility, since each time you run it you will have a different split.
>To make sure to have the same dev set each time this code is run, we need to fix the random seed before shuffling the filenames:

```python
filenames = ['img_000.jpg', 'img_001.jpg', ...]
random.seed(230)
random.shuffle(filenames)

split_1 = int(0.8 * len(filenames))
split_2 = int(0.9 * len(filenames))
train_filenames = filenames[:split_1]
dev_filenames = filenames[split_1:split_2]
test_filenames = filenames[split_2:]
```


### References
TODO: add references
- coursera
- our github


[github]: https://github.com/cs230-stanford/cs230-starter-code
<!-- TODO: put correct link -->
[tf-post]: https://cs230-stanford.github.io/
<!-- TODO: put correct link -->
[tf-vision]: https://cs230-stanford.github.io/
<!-- TODO: put correct link -->
[tf-nlp]: https://cs230-stanford.github.io/