---
title: GeoCodeNet
description: Perform GeoCoding with Deep Learning 
#date: "2019-05-02T19:47:09+02:00"
jobDate: 2020
work: [deeplearning, geocode]
techs: [deeplearning, geocode]
#designs: [Photoshop]
thumbnail: geocodenet/sample.jpg
projectUrl: https://github.com/scampion/geocodenet
#testimonial:
#  name: John Doe
#  role: CEO @Example
#  image: sample-project/john.jpg
#  text: Prow scuttle parrel provost Sail ho shrouds spirits boom mizzenmast yardarm. Pinnace holystone mizzenmast quarter crow's nest nipperkin
---

### Cite as:   

Sebastien Campion. (2020, November 14). GeocodeNet (Version 1.0.0). Zenodo. http://doi.org/10.5281/zenodo.4273715

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4273715.svg)](https://doi.org/10.5281/zenodo.4273715)


    /!\ Work in progress, Bi-directionnal RNN network must be evaluate 


## Objective 

A geocode operation is a process to translate a geographic entity in a code. 

For example, we want to translate this french address : 

    Route du Tréport 80230 Saint-Valery-sur-Somme
    
Into geographic coordinate, longitude and latitude : 

    47,0.504549603611111 0.7787510938888894
    
## Issue 

When the address is misspelled or contain error, it's much more difficult to find coordinate. 
You may also want to transform address into linear values in order to use it in another machine learning process.

With our previous exemple, it could be something like that: 

    Rte du trepot saint-valery / somme
    

## Character Convolutions Neural Network to the rescue

To solve that issue, we will use the Character-level Convolutional Networks for Text Classification detailed here : 

Xiang Zhang, Junbo Zhao, Yann LeCun. [Character-level Convolutional Networks for Text Classification](http://arxiv.org/abs/1509.01626). NIPS 2015

We will modify the neural network to learn multi-output regression instead of classification. 


We will also augment the train dataset by added, synthetically generate as many, random permutations:
- Character substitution (using nearby characters on the keyboard)
- Deletion
- Transposition
- Duplication

This step of learn on augmented data is not yet implemented

<a href="https://colab.research.google.com/github/scampion/geocodenet/blob/main/geocodenet.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

Nota bene: An other approach based on ngrams, TF-IDF, dimension reduction with T-SVD and ANN is also available in the 
[opengeocode project](https://github.com/scampion/opengeocode/blob/master/tfidf_nn.py)


