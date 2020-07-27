# Patient Cohort Identification with Komenti

Komenti is a shiny and multi-faceted tool, bringing together the gnoses of biomedical ontology and the delights of text mining. In this protocol document, we will describe how to use Komenti to characterise a patient cohort. 

## Setting up Komenti

Refer to the [installation guide](https://github.com/reality/komenti_guide/blob/master/installation.md).

## Initialising Vocabulary

To characterise our cohort of interest, we first need to build up a vocabulary. The vocabulary defines concepts we're interested in, and the labels that indicate them in text. In our example, let's say we're interested in finding patients with atrial fibrillation.

First, what we need to do is identify an ontology class that describes atrial fibrillation on [AberOWL](https://aber-owl.net). Here is an example of searching AberOWL for atrial fibrillation: 

![image](https://user-images.githubusercontent.com/223469/87681045-10ca8e80-c776-11ea-92b7-b145f7db8a06.png)

We have terms grouped by their unique identifiers, and we need to select one which is common and ideally from a major ontology. For human phenotypes and diseases HP, DOID, and NCIT are good choices. So, in this case we can see that HP comes up first. We need to copy this IRI into a Komenti command, to query it *<http://purl.obolibrary.org/obo/HP_0005110>*:

```bash
komenti query -q "<http://purl.obolibrary.org/obo/HP_0005110>" -o HP --out labels.txt --expand-synonyms --override-group "atrial fibrillation"
```

In the `-q` argument you specify your class (you can actually also pass the label in there!), and in `-o` you specify the ontology. These both correspond to the values we took from AberOWL just now.

This command will populate labels.txt with your labels. If you run `head labels.txt` you'll see something like this:

```tsv
permanent atrial fibrillation   http://purl.obolibrary.org/obo/HP_0004754       atrial fibrillation     HP      1
paroxysmal atrial fibrillation  http://purl.obolibrary.org/obo/HP_0004757       atrial fibrillation     HP      1
atrial fibrillation, paroxysmal http://purl.obolibrary.org/obo/HP_0004757       atrial fibrillation     HP      1
atrial fibrillation     http://purl.obolibrary.org/obo/HP_0005110       atrial fibrillation     HP      1
quivering upper heart chambers resulting in irregular heartbeat http://purl.obolibrary.org/obo/HP_0005110       atrial fibrillation     HP      1
...
```

This is the beginning of the vocabulary. The query retrieved all subclasses of the atrial fibrillation class in HP, and all their. Since you passed `--expand-synonyms`, it will also gain additional synonyms from other biomedical ontologies. Because we used the `--override-group`, we also have a helpful identifier for which condition we're interested in.

This allows us to build a single vocabulary for associated conditions. Say we're also interested in whether people with atrial fibrillation have amyloidosis. We can look that up to find there's a good characterisation of that in DO. We can query for that by name, and append it to our vocabulary:

```bash
komenti query -q "amyloidosis" -o DOID --out labels.txt --expand-synonyms --override-group "amyloidosis" --append
```

What's changed here, is that we're passing a named query with the `-q` argument, instead of an IRI. We also changed the `--override-group` to reflect the new condition. The other major change is that we pass `--append`. This appends the results of this query to the existing `labels.txt`, instead of replacing it.


## Document Preparation

Now that we have our vocabulary, we have to collect our documents. This will depend a bit on what you want, what your outputs are, and what format you have your data in. If you're mining literature, it's likely that you are going to be looking at one file per entity, while for patients you may have many documents describing one patient. 

In this case, let's assume that we have lots of PDF files describing patients. Patients can have many PDF files each. What we want to do, is find files that match our atrial fibrillation keywords (which you can now retrieve from the `atrial fibrillation` file). Then, we obtain the patient identifier, and lay out our files like this

```
af_letters/
  id1/
    biscuit.pdf
    ohjsdaiofdsa.pdf
  id2/
    1.pdf
    2.pdf
    3.pdf
  id3/
    bla.pdf
```

The reason for this is two-fold: we want to pre-select files that contain our keywords of interest (otherwise Komenti will be too slow), and we want to collect judgements about the same patients from multiple files together. What the files themselves are called is not important, but having an overall directory structure that contains the patient IDs is important.

## Document Characterisation

First you want to annotate the letters with our vocabulary:

```bash
$ komenti annotate -l labels.txt -t af_letters/ --out annotations.txt --group-directory-files
```

This will recursively annotate all of the files, collecting the annotations into a group based on the sub-directory. In this case, the identification number for the patient.

Then, we will use the decision procedure to decide, per document, who has each disease:

```bash
$ komenti diagnose -l labels.txt -a annotations.txt --by-group
```

This will give you a TSV containing the judgements.

## Manual Validation

TODO



