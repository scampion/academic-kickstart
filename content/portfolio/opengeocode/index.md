---
title: OpenGeoCode
description: Perform GeoCoding with ANN, ngrams and TDIDF 
#date: "2019-05-02T19:47:09+02:00"
jobDate: 2020
work: [ann, tdfidf, geocode]
techs: [ann, tdfidf, geocode]
#designs: [Photoshop]
thumbnail: opengeocode/opengeocode.png
projectUrl: https://github.com/scampion/opengeocode
#testimonial:
#  name: John Doe
#  role: CEO @Example
#  image: sample-project/john.jpg
#  text: Prow scuttle parrel provost Sail ho shrouds spirits boom mizzenmast yardarm. Pinnace holystone mizzenmast quarter crow's nest nipperkin
---

### Cite as:   

Sebastien Campion. (2020, November 14). OpenGeoCode (Version 1.0). http://doi.org/10.5281/zenodo.4273721

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4273721.svg)](https://doi.org/10.5281/zenodo.4273721)


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
    
## NGrams, TF-IDF, Truncated Singular Value Decomposition and Nearest Neighbors

In our case we will use trigrams of characters to solve misspelling address and more generally robustifying 
the search index.  `computer` will be translate in `com`, `omp`, `mpu`, `put`, `ute`, `ter`

TDIDF will produce a sparse vector representation of the address. 
To speed compute result in search operation, you can use a Truncated Singular Value Decomposition appropriate for 
these sparse tfidf matrix. 

Lastly a Nearest Neighbour with ball tree algorithm provide the most similar values.

 




