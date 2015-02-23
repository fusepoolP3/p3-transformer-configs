Toscana Event Transformer Configuration
==========================================

[RML](https://github.com/mmlab/RMLProcessor) based transformer configuration for the Toscana event data [Regione Toscana - eventi sistema cultura - area musei](http://dati.toscana.it/dataset/rt-eventi-sistcult).

The data are about events in the Province of Toscana. The vocabularies used in the mapping are:  

    rdf    <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    rdfs   <http://www.w3.org/2000/01/rdf-schema#>  
    wgs84  <http://www.w3.org/2003/01/geo/wgs84_pos#>
    xsd    <http://www.w3.org/2001/XMLSchema#>  
    schema <http://schema.org/>

For each event the following information are extracted and mapped to RDF

- Title of the events (Italian)  
- Description of the event (Italian)
- Address  
- Start and end dates  
- Organizer

The transformer that can be used to transform the XML data into RDF is the [TODO](TODO) and must use `mapping.ttl` as its configuration file.

