# Trentino Taverns dataset

## Input Data

[http://dati.trentino.it/dataset/osterie-tipiche-trentine]

[http://www.commercio.provincia.tn.it/binary/pat_commercio/marchi_prodotto/Elenco_osterie_tipiche_civici.1386925759.csv]

This CSV dataset contains the names and addresses of typical Trentino taverns. The classification of 'typical Trentino' guarantees the quality of the food and wine on offer, and that the produce served originates mainly from Trento. The type of each tavern is included, the types being: Ristorante, Ristorante-Bar, Albergo-Ristorante-Bar, Ristorante - Pizzeria.

```csv
Nr,Comune,Insegna,Tipo,Frazione,Indirizzo,Civico
1,Andalo,Al Faggio,Ristorante,Andalo,Via Fovo,11
2,Arco,Osteria Il Ritratto,Ristorante-Bar,Arco,Via Ferrera,30
3,Baselga Di Pine,Ai Due Cami,Albergo-Ristorante-Bar,Baselga Di Pine,Via Pontara,352
```

## Transformed Data
The source data has been transformed to RDF using the Virtuoso CSV transformer (http://fusepool.openlinksw.com/ext/csv). The generated RDF is attached below as RDF/XML and Turtle. The only cleaning of the source data performed was the conversion of the originally uppercase tavern names to title case.

```bash
curl -H "Content-Type: text/csv"  -H "Content-Location: http://fusepool.openlinksw.com/trento/pub" --data-binary @Elenco_osterie_tipiche_civici.1386925759.cleaned.csv -X POST "http://fusepool.openlinksw.com/ext/csv" > Elenco_osterie_tipiche_civici.1386925759.csv.transformed.fusepool.ttl
```

```bash
curl -H "Content-Type: text/csv"  -H "Accept: application/rdf+xml" -H "Content-Location: http://fusepool.openlinksw.com/trento/pub" --data-binary @Elenco_osterie_tipiche_civici.1386925759.cleaned.csv -X POST "http://fusepool.openlinksw.com/ext/csv" > Elenco_osterie_tipiche_civici.1386925759.csv.transformed.fusepool.rdf
```

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

The Virtuoso CSV transformer requires no end-user configuration, insofar as the transformation is expressed in a developer-defined XSLT stylesheet (trentopubs2rdf.xsl). The stylesheet and supporting Virtuoso/PL code are packaged and installed via a Fusepool-specific Cartridges VAD. Use of a particular stylesheet for transformation of a specific CSV dataset is triggered by associating the header line of the CSV data with the .xsl stylesheet. This association is set up through the 'CSV Pattern configuration' dialog in Virtuoso's Conductor UI, as depicted in the screenshots below.

!csv_header_config1.png!

!csv_header_config2.png!

## Example Usages of the Data

The "Trentino Typical Pubs" dataset archive could provide the basis for online services supporting the searching and booking of local taverns.

*TODO* Describe how the result data can be used (e.g. by a SPARQL query). This SHOULD also include examples on how this dataset can be combined with other data.
