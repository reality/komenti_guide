# Patient Cohort Identification with Komenti

## Setting up Komenti

```bash
cd ~
wget https://github.com/reality/komenti/releases/download/0.1.1/komenti-0.1.1.zip
unzip komenti-0.1.1.zip
echo 'echo PATH=$PATH:~/komenti/bin' >> ~/.bashrc
```

## Initialising Vocabulary

To characterise our cohort of interest, we first need to build up a vocabulary. The vocabulary defines concepts we're interested in, and the labels that indicate them in text. In our example, let's say we're interested in finding patients with atrial fibrillation.

First, what we need to do is identify an ontology class that describes atrial fibrillation on [AberOWL](https://aber-owl.net). You can 



## Document Preparation

This will depend a bit on what you want, what your outputs are, and what format you have your data in. If you're mining literature, it's likely that you are going to be looking at one file per entity, while. 

The performance of Komenti depends quite a lot on how well-formed the input text is. 

## Document Characterisation

First you want to annotate the letters. 

```bash
$ komenti annotate -l labels.txt -t texts/ --out annotations.txt
```

Then, we will use the decision procedure to decide, per document, who has the disease

```bash
$ komenti diagnose -l labels.txt -a annotations.txt
```

## Manual Validation

