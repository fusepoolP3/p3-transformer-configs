Trentino Pharmacies Transformer Configuration
==========================================

The p3-batchrefine transformer takes a CSV file as input data and a JSON file as transformation rules to RDF. The input data is available online from the Open Data portal of the Province of Trento at the link

    http://dati.trentino.it/dataset/farmacie-pat

The data are about the events in the Province of Trento. The vocabularies used in the mapping are  

    rdf <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    rdfs <http://www.w3.org/2000/01/rdf-schema#>  
    geo <http://www.w3.org/2003/01/geo/wgs84_pos#>
    xsd <http://www.w3.org/2001/XMLSchema#>  
    schema <http://schema.org/>

For each pharmacy the following information are extracted and mapped to RDF  
- Title of the events (English and Italian)  
- Description of the event (English and Italian)  
- Address  
- Geographic coordinates (lat, long)  
- Start and end dates  
- Organizer

The transformer to use with the data set and its configuration file are show in the table

| Data set URL                                       | Transformer       | Config file     |  
|----------------------------------------------------|-------------------|-----------------|  
| [Trentino Pharmacies](http://dati.trentino.it/dataset/farmacie-pat) |[p3-batchrefine](https://github.com/fusepoolP3/p3-batchrefine)|trentino-pharmacies.json|  


A README file with instructions on how to use the transformer is available at the transformer repository on Github. An example of the transformation result is given below  

    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
    @prefix geo: <http://www.w3.org/2003/01/geo/wgs84_pos#> .
    @prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
    @prefix schema: <http://schema.org/> .

    <urn:uuid:fusepoolp3:pharmacy:4054> a schema:Pharmacy ;  
         schema:legalName "SILVESTRI LUCIANA" ;  
         geo:lat "46.4123540693265"^^xsd:double ;  
         geo:long "11.0520541954953"^^xsd:double .  

    <urn:uuid:fusepoolp3:pharmacy:address:0> a schema:PostalAddress ;  
         schema:streetAddress "VIA J. A. MAFFEI, 8" ;  
         schema:addressLocality "REVO'" ;  
         schema:addressRegion "PROV. AUTON. TRENTO" ;  
         schema:postalCode "38028" .  

    <urn:uuid:fusepoolp3:pharmacy:4054> schema:streetAddress <urn:uuid:fusepoolp3:pharmacy:address:0> ;  
         rdfs:label "Farmacia SILVESTRI LUCIANA" .  
