Configuration
=============

## Configuring a pipeline

When defining a Pipeline, a configuration is loaded into the ConfigurationManager, which is passed to the Manager to actually execute the pipeline:

```
ConfigurationManager config = ConfigurationManager.getInstance("configuration.yml");
Manager manager = new Manager(config);	
```

What follows is the description of the default configuration provided as an example. The YAML file containing the main configuration (in this case, [configuration.yml](src/main/resources/configuration.yml)) looks like this:

```
ALDAPA_CONFIG_FILE: configuration/aldapa-default-configuration.yml
TRIPLE_STORE_CONFIG_FILE: configuration/triple-store-default-configuration.yml
TRANSFORMER_CONFIG_FILE: configuration/ejie-calidad-aire-transformer-configuration.yml
```
Those are simply pointers to other configuration files (the tokens should be respected, but the paths can be modified):

* `ALDAPA_CONFIG_FILE`: it contains the base URIs to generate URIs for projects, catalogs, datasets, and named graphs (they are generated by concatenating these base URIs with the names provided by the user).
* `TRIPLE_STORE_CONFIG_FILE`: the configuration for the Storage plugin.
* `TRANSFORMER_CONFIG_FILE`: the configuration for the transformer plugin.

The ALDAPA config file is simply a list of URIs:

```
name: default
comment: A configuration suitable for most cases
author: Mikel Egana Aranguren
PROJECT_BASE: "http://lod.eurohelp.es/aldapa/project/"
CATALOG_BASE: "http://lod.eurohelp.es/aldapa/catalog/"
DATASET_BASE: "http://lod.eurohelp.es/aldapa/dataset/"
GRAPH_BASE: "http://euskadi.eus/graph/"
```

The Triple Store configuration file contains mainly the plugin qualified name:

```
name: InMemoryRDF4JForTests
comment: in memory triple store that will be dumped when the program finishes, only for testting purposes
author: Mikel Egana Aranguren
pluginClassName: es.eurohelp.lod.aldapa.impl.storage.MemoryRDFStore
```

Other plugins will have more info here, like url, user, passwd, etc. That information is up to the implementer, as long as the token `pluginClassName` is respected.

The transformer plugin configuration file has a similar structure (again, other transformation plugins might have a more complex structure):

```
name: EJIECalidadDelAireTransformer
comment: a CSV2RDF transformer for EJIE Calidad del Aire datasets, only for testing purposes
author: Mikel Egana Aranguren
pluginClassName: es.eurohelp.lod.aldapa.impl.transformation.ejiecalidadaire.EJIECalidadAireConverter
```

## Beware

When a new configuration is provided the files mentioned above (`configuration.yml` and the ones it points to) should be modified. The files under `model/` should not be modified, since they are used by ALDAPA to add the basic backbone: however, new triples can be added. For example, the file used to add projects contains:

```
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX foaf:<http://xmlns.com/foaf/0.1/>

PROJECT_URI rdf:type foaf:Project .
```

ALDAPA resolves the `PROJECT_URI` token and adds the appropriate RDF. That means that a user can modify this file, without deleting the triples, like:

```
PREFIX rdf:<http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX foaf:<http://xmlns.com/foaf/0.1/>
PREFIX ex:<http://example.com/>

PROJECT_URI rdf:type foaf:Project .
PROJECT_URI rdf:type ex:Framework .
```

The new triple will be added to every project created. More metadata will be added by default in next releases. 

---
[Back to index](index.md)
