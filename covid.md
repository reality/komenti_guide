# Literature Review with Komenti

In this guide, we will be concerned with the COVID-19 ontology, as it lives on http://www.aber-owl.net/ontology/COVID-19/

## Querying for Concept Descriptions

In the COVID-19 ontology, many different aspects of COVID-19 are discussed. Of these, *COVID Clinical Aspect (COVID:0000402)*. However, only one subclass of this is 
actually asserted in the ontology. We can see this if we run the following Komenti command:

```bash
$ komenti query -q "COVID clinical aspect" -o COVID-19
```

However, if we look on the AberOWL website, there is a 
[wealth of classery below this term](http://www.aber-owl.net/ontology/COVID-19/#/Browse/%3Chttps%3A%2F%2Fbio.scai.fraunhofer.de%2Fontology%2FCOVID_0000402%3E). This is
because they are inferred subclasses. They are inferred because terms we can see under COVID Clinical Aspect contain a subclassof axiom: *isClinicalAspectFor some COVID-19*. 
They are inferred as subclasses because *COVID Clinical Aspect (COVID:0000402)* contains an equivalency axiom for *isClinicalAspectFor some COVID-19*. Komenti can
query these axioms and their subclasses directly:

```bash
$ komenti query -q "isClinicalAspectFor some COVID-19" -o COVID-19
```

This will return a list of hundreds of terms across a wide range of specialities. Using the asserted and inferred structures of the ontologies that assert the original 
ontologies to further specify the set of terms that we're interested in:

```bash
$ komenti query -q komenti query -q "isClinicalAspectFor some COVID-19 and 'therapeutic procedure'" -o COVID-19 > labels.txt
```

The command collects different metadata associated with terms that are all clinical aspects of COVID-19, but are contained under different asserted substructures. Grouping them
by their additional associated superclasses 'therapeutic procedure' we group all of these into one with the class description. Now, in *labels.txt* we have 6 labels for 5 different
terms that describe therapeutic procedures for COVID-19. We can gain more labels for this by using Komenti's inter-ontology synonym expansion function:

```bash
$ komenti query -q komenti query -q "isClinicalAspectFor some COVID-19 and 'therapeutic procedure'" -o COVID-19 --expand-synonyms --lemmatise > labels.txt
```

This gives us some additional labels, including *kidney replacement therapy* for the *renal replacement therapy (NCIT:C126400)* class. These additional labels will allow us to recognise even more of these items in text!

## Acquiring Content for Review

Now that we have some labels in our *labels.txt* that describe therapeutic procedures, we can start to query EBI's PMC dataset using Komenti. For ease, we will work with this small set of labels, and only on abstracts.

```bash
$ komenti get_abstracts -l labels.txt --out abstracts/
```

This will download the abstracts of matching articles in PubMed into the *abstracts/* folder. This would give us an initial start to any manual literature review.

## Annotation

we can further use Komenti to associate the abstracts with the concepts we're interested in, by annotating them:

```bash
$ komenti annotate -l labels.txt -t abstracts/ --out ann.txt
```

This produces an *ann.txt* file, which is a list of every mention of each of our concepts of interest, with its surrounding sentence as context. For example:

```
 203 PMC7291880.txt  http://purl.obolibrary.org/obo/NCIT_C94624  oxygen therapy  supplemental oxygen therapy isClinicalAspectFor some COVID-19 and 'therapeutic procedure'       8   most patients (23/29; 79.8     %) received supplemental oxygen therapy and antibiotics (23/29; 79.8%) in addition to traditional chinese medicines (26/29; 89.7%).
```

In this way, we are building assocaitions between our original complex class descriptions, and actual text. 

## Sumamrisation

With this command, you can produce a report, grouping articles by topics in the vocabulary:

```bash
$ komenti summarise -a ann.txt
```

## Entity occurrence

We can further extend the functionality we have thus investigated to look at co-ocurrence between groups of concepts or complex class descriptions:

```bash
komenti summarise_entity_pairs -l labels.txt -a annotation.txt -c asbestos,toxicity
```
