# Transformer configuration for the Trentino taverns (Osterie) dataset

## Input Data

As input data we have a CSV file provide by the Trento region, publicly available at [dati.trentino.it](http://dati.trentino.it/dataset/osterie-tipiche-trentine) -direct-link-> [Elenco_osterie_tipiche_civici.1386925759.csv](http://www.commercio.provincia.tn.it/binary/pat_commercio/marchi_prodotto/Elenco_osterie_tipiche_civici.1386925759.csv)

This CSV dataset contains the names and addresses of typical Trentino taverns. The classification of 'typical Trentino' guarantees the quality of the food and wine on offer, and that the produce served originates mainly from Trento. The type of each tavern is included, the types being: Ristorante, Ristorante-Bar, Albergo-Ristorante-Bar, Ristorante - Pizzeria.

A snippet of the data is presented below (Header and 3 consecutive lines):


```csv
Nr;Comune;Insegna;Tipo;Frazione;Indirizzo ;Civico
1;ANDALO;AL FAGGIO ;Ristorante;Andalo;Via Fovo;11
2;ARCO;OSTERIA IL RITRATTO;Ristorante-Bar;Arco;Via Ferrera;30
3;BASELGA DI PINE';AI DUE CAMI;Albergo-Ristorante-Bar;Baselga Di Pine';Via Pontara;352
4;BASELGA DI PINE';AL POSTA HOTEL - RISTORANTE CA' DEI BOCI ;Albergo-Ristorante-Bar;Montagnaga,  Loc. Cadrobbi;Via Targa;1
5;BESENELLO;LA RUPE DI BESE;Ristorante-Bar;Besenello;Via Castel Beseno;6
8;CALAVINO;HOSTERIA TOBLISELLIPOINT;Ristorante-Bar;Sarche;Via Garda;3
```
By looking at the raw data we can make several judgments:

* it is a reasonably small dataset, having only 48 entries (lines)
* the input dataset is structured correctly and doesn't contain errors

However there are two remarks about the content of the dataset:
* in the 'Nr' column, the values doesn't always show the correct line number. As we can see in the snippet above, the numbering order goes 4,5,8;
* entries in the 'Comune' and 'Insegna' columns are all in uppercase.

We apply two Fusepool transformers on this dataset:

1. **Cleaning:** [Batchrefine transformer](https://github.com/fusepoolP3/p3-batchrefine) to align the values in 'Nr' column with the real line number and to convert values in 'Comune', 'Insegna' columns to titlecase.

2. **Transform to RDF:** Virtuoso RDF transformer http://fusepool.openlinksw.com/ext/csv is used to transform the cleaned dataset into RDF. Data can be transformed both to RDF/XML and Turtle. 

## Transformed Data

As far as possible the input has been mapped to schema.org types.
* Addresses are mapped to schemaOrg:PostalAddress
* The original tavern type is mapped to one or more of schemaOrg:BarOrPub, schemaOrg:FastFoodRestaurant, schemaOrg:Hotel or schemaOrg:Restaurant as appropriate. 

```rdf
<rdf:RDF
 	...
	xmlns:sioc="http://rdfs.org/sioc/ns#"
	xmlns:opl="http://www.openlinksw.com/schema/attribution#"
	xmlns:schema="http://schema.org/"
	xmlns:oplbase="http://www.openlinksw.com/schemas/oplbase#" >

   <rdf:Description rdf:about="http://fusepool.openlinksw.com/trento/pub#Organization_1">
    <rdf:type rdf:resource="http://schema.org/Restaurant" />
    <rdfs:label>Al Faggio</rdfs:label>
    <schema:name>Al Faggio</schema:name>
    <sioc:has_container rdf:resource="http://fusepool.openlinksw.com/about/id/entity/http/fusepool.openlinksw.com/trento/pub" />
    <oplbase:id>1</oplbase:id>
    <schema:address rdf:resource="http://fusepool.openlinksw.com/trento/pub#Organization_1_Addr" />
    <schema:description>Ristorante</schema:description>
  </rdf:Description>

  <rdf:Description rdf:about="http://fusepool.openlinksw.com/trento/pub#Organization_2">
    <rdf:type rdf:resource="http://schema.org/Restaurant" />
    <rdf:type rdf:resource="http://schema.org/BarOrPub" />
    <rdfs:label>Osteria Il Ritratto</rdfs:label>
    <schema:name>Osteria Il Ritratto</schema:name>
    <sioc:has_container rdf:resource="http://fusepool.openlinksw.com/about/id/entity/http/fusepool.openlinksw.com/trento/pub" />
    <oplbase:id>2</oplbase:id>
    <schema:address rdf:resource="http://fusepool.openlinksw.com/trento/pub#Organization_2_Addr" />
    <schema:description>Ristorante-Bar</schema:description>
  </rdf:Description>

  <rdf:Description rdf:about="http://fusepool.openlinksw.com/trento/pub#Organization_3">
    <rdf:type rdf:resource="http://schema.org/Hotel" />
    <rdf:type rdf:resource="http://schema.org/BarOrPub" />
    <rdf:type rdf:resource="http://schema.org/Restaurant" />
    <rdfs:label>Ai Due Cami</rdfs:label>
    <schema:name>Ai Due Cami</schema:name>
    <sioc:has_container rdf:resource="http://fusepool.openlinksw.com/about/id/entity/http/fusepool.openlinksw.com/trento/pub" />
    <oplbase:id>3</oplbase:id>
    <schema:address rdf:resource="http://fusepool.openlinksw.com/trento/pub#Organization_3_Addr" />
    <schema:description>Albergo-Ristorante-Bar</schema:description>
  </rdf:Description>

  <rdf:Description rdf:about="http://fusepool.openlinksw.com/trento/pub#Organization_1_Addr">
    <rdf:type rdf:resource="http://schema.org/PostalAddress" />
    <rdfs:label>Via Fovo, 11; Andalo</rdfs:label>
    <schema:streetAddress>Via Fovo, 11</schema:streetAddress>
    <schema:addressLocality>Andalo</schema:addressLocality>
    <schema:Country>IT</schema:Country>
  </rdf:Description>

  <rdf:Description rdf:about="http://fusepool.openlinksw.com/trento/pub#Organization_2_Addr">
    <rdf:type rdf:resource="http://schema.org/PostalAddress" />
    <rdfs:label>Via Ferrera, 30; Arco</rdfs:label>
    <schema:streetAddress>Via Ferrera, 30</schema:streetAddress>
    <schema:addressLocality>Arco</schema:addressLocality>
    <schema:Country>IT</schema:Country>
  </rdf:Description>

  <rdf:Description rdf:about="http://fusepool.openlinksw.com/trento/pub#Organization_3_Addr">
    <rdf:type rdf:resource="http://schema.org/PostalAddress" />
    <rdfs:label>Via Pontara, 352; Baselga Di Pine</rdfs:label>
    <schema:streetAddress>Via Pontara, 352</schema:streetAddress>
    <schema:addressLocality>Baselga Di Pine</schema:addressLocality>
    <schema:Country>IT</schema:Country>
  </rdf:Description>
```


## Transformation Configuration

#### 1. Clean and prepare data

**Cleaning** with [Batchrefine](https://github.com/fusepoolP3/p3-batchrefine)

* change entries in columns 'Comune' and 'Insegna' to titlecase: **BASELGA DI PINE' --> Baselga Di Pine'**
* fix line number in 'Nr' column

To use [Batchrefine transformer](https://github.com/fusepoolP3/p3-batchrefine) a transform configuration is required. We use GUI of [OpenRefine](https://github.com/OpenRefine/OpenRefine) to prepare transformation rules that will be further passed to the transformer in a query parameter.
A brief tutorial how to design and extract transformation rules from OpenRefine that can be found [here](https://github.com/andreybratus/tutorial).

Transformation rules are extracted from OpenRefine and saved in a JSON file, which we also provide: [osterie-transform.json](osterie-transform.json)

Pass transformation rules as a 'refinejson' query parameter to batchrefine together with the input data as shown below:

```bash
curl -i -XPOST -H 'Content-Type:text/csv' -H 'Accept:text/csv' --data-binary @Elenco_osterie_tipiche_civici.1386925759.csv "http://hetzy1.spaziodati.eu:7100/?refinejson=https://raw.githubusercontent.com/fusepoolP3/p3-transformer-configs/master/trentino-restaurants/osterie-transform.json"
```

http://hetzy1.spaziodati.eu:7100 is a public instance of asynchronous Batchrefine transformer, which would return similar response:

```
HTTP/1.1 100 Continue

HTTP/1.1 202 Accepted
Date: Thu, 29 Jan 2015 15:14:54 GMT
Location: /job/b75afaef-9a26-4cf6-b8f1-78a272b9ff66
Transfer-Encoding: chunked
Server: Jetty(9.2.z-SNAPSHOT)
```

to retrieve data, construct the following request using the job id from Location header: **REMEMBER to use your job id**

```bash
	curl -XGET "http://hetzy1.spaziodati.eu:7100/job/b75afaef-9a26-4cf6-b8f1-78a272b9ff66"
```
expected result after cleaning is [Elenco_osterie_tipiche_civici.1386925759.cleaned.csv](Elenco_osterie_tipiche_civici.1386925759.cleaned.csv), which we will use to pass to Virtuoso CSV transformer.

#### 2. Transform cleaned data to RDF

After cleaning the dataset, we use the **Virtuoso CSV transformer** (http://fusepool.openlinksw.com/ext/csv) to transform it to RDF. The generated RDF can be requested as RDF/XML or Turtle (Example requests for both output types are shown below).

**Trutle**
```bash
curl -i -H "Content-Type: text/csv" -H "Accept: text/turtle"  -H "Content-Location: http://fusepool.openlinksw.com/trento/pub" --data-binary @Elenco_osterie_tipiche_civici.1386925759.cleaned.csv -X POST "http://fusepool.openlinksw.com/ext/csv" > Elenco_osterie_tipiche_civici.1386925759.csv.transformed.fusepool.ttl
```

**RDF/XML**
```bash
curl -i -H "Content-Type: text/csv"  -H "Accept: application/rdf+xml" -H "Content-Location: http://fusepool.openlinksw.com/trento/pub" --data-binary @Elenco_osterie_tipiche_civici.1386925759.cleaned.csv -X POST "http://fusepool.openlinksw.com/ext/csv" > Elenco_osterie_tipiche_civici.1386925759.csv.transformed.fusepool.rdf
```
which would return similar response:

```
HTTP/1.1 100 Continue

HTTP/1.1 202 Accepted
Server: Virtuoso/07.10.3211 (Linux) x86_64-redhat-linux-gnu  VDB
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8
Date: Fri, 30 Jan 2015 19:15:36 GMT
Accept-Ranges: bytes
Location: /ext/status/120
Content-Length: 
```

to retrieve data, construct the following request using the job id from Location header: **REMEMBER to use your job id**

```bash
curl -X GET "http://fusepool.openlinksw.com/ext/status/120"
```

The expected result is [Elenco_osterie_tipiche_civici.1386925759.csv.transformed.fusepool.ttl](Elenco_osterie_tipiche_civici.1386925759.csv.transformed.fusepool.ttl) or [Elenco_osterie_tipiche_civici.1386925759.csv.transformed.fusepool.rdf](Elenco_osterie_tipiche_civici.1386925759.csv.transformed.fusepool.rdf)

The Virtuoso CSV transformer requires no end-user configuration, and so far as the transformation is expressed in a developer-defined XSLT stylesheet (trentopubs2rdf.xsl). The stylesheet and supporting Virtuoso/PL code are packaged and installed via a Fusepool-specific Cartridges VAD. Use of a particular stylesheet for transformation of a specific CSV dataset is triggered by associating the header line of the CSV data with the .xsl stylesheet. This association is set up through the 'CSV Pattern configuration' dialog in Virtuoso's Conductor UI, as depicted in the screenshots below.

<div align="center">
<img src="img/csv_header_config1.png" alt="Header config" width="75%" height=75%>
</div>

<div align="center">
<img src="img/csv_header_config2.png" alt="Header config" width="75%" height=75%>
</div>

## Example Usages of the Data

Though the Trentino taverns dataset is of a modest size and provides scarce information, it represents venues that serve typical food of Trentino region. It can be used as a basis for online services supporting an end user with searching and booking of local taverns and providing a guarantee of the best food quality.

*TODO* Describe how the result data can be used (e.g. by a SPARQL query). This SHOULD also include examples on how this dataset can be combined with other data.
