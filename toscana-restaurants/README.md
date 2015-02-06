# Transformer configuration for Toscana Restaurants dataset

## Input Data

The following dataset contains information about restaurants in Toscana region
[dati.toscana.it](http://dati.toscana.it/dataset/rt-vetrinatoscana/resource/80020491-6b17-4e1b-94be-bd8d1a41f62a). [Direect link](http://dati.toscana.it/dataset/75d6183f-8b8d-4150-a1f0-f69eb984c238/resource/80020491-6b17-4e1b-94be-bd8d1a41f62a/download/esportazioneadesioni10102014.csv)

This CSV dataset contains names, adresses, geolocation and contact information of 2263 restaurants.  A snippet of the original dataset is shown below:

| TIPOLOGIA  | NOME                          | VIA                          | CITTA'                     | CAP   | PROVINCIA | EMAIL                          | TELEFONO   | FAX      | LAT        | LONG       |
|------------|-------------------------------|------------------------------|----------------------------|-------|-----------|--------------------------------|------------|----------|------------|------------|
| RISTORANTE | Bagno Lelia                   | Terrazza della Repubblica 21 | Citt� Giardino, VIAREGGIO  | 55049 | Lucca     | info@bagnolelia.it             | 58450006   | 58450006 | 43.8840098 | 10.2332009 |
| RISTORANTE | Bagno Ristorante il Cavallone | Viale Sergio Bernardini 722  | Lido Di Camaiore, CAMAIORE | 55041 | Lucca     | laura.bagnocavallone@gmail.com | 584610554  |          | 43.9421402 | 10.2969083 |
| RISTORANTE | bagou bally juvenal           | via maromme 30/6             | SIGNA                      | 50058 | Firenze   | ballyjuvenal@gmail.com         | 3927807905 |          | -0         | -0         |

Inspecting oiginal dataset one can easily identify the following problems:

* Entries in Tipologia column are all in uppercase
* Entries in Nome, Via and Citta columns doesn't have a unified case, part of the entries are uppercase, lowercase.
* The dataset has encoding problems, such that letters with accent (á, é, í, ó) are lost and can not be recovered. They are replaced with a '�' unicode replacement character, such that words like _Caffé_, _Localitá_  have a weired symbol instead of accent letter. 
* Whenever a geolocation information is missing for a venue, entries in columns LAT, LONG.

We apply two Fusepool transformers on this dataset:

1. **Cleaning:** [Batchrefine transformer](https://github.com/fusepoolP3/p3-batchrefine) to align the values in 'Nr' column with the real line number and to convert values in 'Comune', 'Insegna' columns to titlecase.

2. **Transform to RDF:** Virtuoso RDF transforme http://fusepool.openlinksw.com/ext/csv is used to transform the cleaned dataset into RDF. Data can be transformed both to RDF/XML and Turtle. 


## Transformed Data

1. Cleaned dataset from Batchrefine

[esportazioneadesioni10102014-cleaned.csv](esportazioneadesioni10102014-cleaned.csv)

| TIPOLOGIA  | NOME                          | VIA                          | CITTA                      | CAP   | PROVINCIA | EMAIL                          | TELEFONO   | FAX      | LAT      | LONG      |
|------------|-------------------------------|------------------------------|----------------------------|-------|-----------|--------------------------------|------------|----------|----------|-----------|
| Ristorante | Bagno Lelia                   | Terrazza Della Repubblica 21 | Citta Giardino, Viareggio  | 55049 | Lucca     | info@bagnolelia.it             | 58450006   | 58450006 | 43.88401 | 10.233201 |
| Ristorante | Bagno Ristorante Il Cavallone | Viale Sergio Bernardini 722  | Lido Di Camaiore, Camaiore | 55041 | Lucca     | laura.bagnocavallone@gmail.com | 584610554  |          | 43.94214 | 10.296908 |
| Ristorante | Bagou Bally Juvenal           | Via Maromme 30/6             | Signa                      | 50058 | Firenze   | ballyjuvenal@gmail.com         | 3927807905 |          |          |           |



2. Transformed to RDF using Virtuoso CSV Transformer

```rdf
  <rdf:Description rdf:about="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_96">
    <rdf:type rdf:resource="http://schema.org/Restaurant" />
    <rdf:type rdf:resource="http://schema.org/Organization" />
    <rdfs:label>Bagno Lelia</rdfs:label>
    <geo:lat>43.88401</geo:lat>
    <geo:long>10.233201</geo:long>
    <schema:name>Bagno Lelia</schema:name>
    <sioc:has_container rdf:resource="http://fusepool.openlinksw.com/about/id/entity/http/fusepool.openlinksw.com/tuscany/restaurant" />
    <oplbase:id>96</oplbase:id>
    <schema:address rdf:resource="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_96_Addr" />
    <schema:description>Ristorante</schema:description>
    <schema:geo rdf:resource="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_96_GeoCoords" />
  </rdf:Description>
  <rdf:Description rdf:about="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_97">
    <rdf:type rdf:resource="http://schema.org/Restaurant" />
    <rdf:type rdf:resource="http://schema.org/Organization" />
    <rdfs:label>Bagno Ristorante Il Cavallone</rdfs:label>
    <geo:lat>43.94214</geo:lat>
    <geo:long>10.296908</geo:long>
    <schema:name>Bagno Ristorante Il Cavallone</schema:name>
    <sioc:has_container rdf:resource="http://fusepool.openlinksw.com/about/id/entity/http/fusepool.openlinksw.com/tuscany/restaurant" />
    <oplbase:id>97</oplbase:id>
    <schema:address rdf:resource="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_97_Addr" />
    <schema:description>Ristorante</schema:description>
    <schema:geo rdf:resource="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_97_GeoCoords" />
  </rdf:Description>
  <rdf:Description rdf:about="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_98">
    <rdf:type rdf:resource="http://schema.org/Restaurant" />
    <rdf:type rdf:resource="http://schema.org/Organization" />
    <rdfs:label>Bagou Bally Juvenal</rdfs:label>
    <geo:lat></geo:lat>
    <geo:long></geo:long>
    <schema:name>Bagou Bally Juvenal</schema:name>
    <sioc:has_container rdf:resource="http://fusepool.openlinksw.com/about/id/entity/http/fusepool.openlinksw.com/tuscany/restaurant" />
    <oplbase:id>98</oplbase:id>
    <schema:address rdf:resource="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_98_Addr" />
    <schema:description>Ristorante</schema:description>
    <schema:geo rdf:resource="http://fusepool.openlinksw.com/tuscany/restaurant#Organization_98_GeoCoords" />
  </rdf:Description>
```

[RDF](esportazioneadesioni10102014-transformed.rdf)

[Turtle](esportazioneadesioni10102014-transformed.ttl)
