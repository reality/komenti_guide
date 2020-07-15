# Patient Cohort Identification with Komenti

## Setting up Komenti

```bash
cd ~
wget https://github.com/reality/komenti/releases/download/0.1.1/komenti-0.1.1.zip
unzip komenti-0.1.1.zip
echo 'echo PATH=$PATH:~/komenti/bin' >> ~/.bashrc
```

## Initialising Vocabulary

For each condition. So, let's say you're interested in 

## Document Preparation

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

