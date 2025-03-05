Custom SkroutzGreekStemmer plugin for ElasticSearch 7.17.9
==========================================================

Versions
--------

SkroutzGreekStemmer Plugin | ElasticSearch | Branch
---------------------------|---------------|--------|
7.7.0.2                    | 7.17.9        | main   |


Local Setup
-----------
1) Build the Project: Run the following Maven command to clean and install the project:
```mvn clean install```

2) Package the Plugin: Build the plugin package with the following Maven command:
```mvn package```

3) Create the Plugin Zip: Create a .zip file containing the JAR and the plugin-descriptor.properties file. Ensure both files are included in the archive.

4) Install the Plugin: Install the plugin by executing the following command:
```/usr/share/elasticsearch/bin/elasticsearch-plugin install file:<path-to-file>/plugin.zip```
Restart Elasticsearch: After the plugin is installed, restart the Elasticsearch 7.17.9 instance to load the SkroutzGreekStemmer plugin.

Note: The plugin.zip file already exists in the plugin/ folder.


Example usage
-------------

    # Create index
    $ curl -XPUT 'http://localhost:9200/test_stemmer' -H 'Content-Type: application/json' -d '{
      "settings":{
        "analysis":{
          "analyzer":{
            "stem_analyzer":{
              "type":"custom",
                "tokenizer":"standard",
                "filter": ["lower_greek", "stem_greek"]
            }
          },
          "filter": {
            "lower_greek": {
              "type":"lowercase",
              "language":"greek"
            },
            "stem_greek": {
              "type":"skroutz_stem_greek"
            }
          }
        }
      }
    }'
    {"acknowledged":true}

    # Test analyzer
    $ curl -XGET 'http://localhost:9200/test_stemmer/_analyze?pretty' -H 'Content-Type: application/json' -d '{"analyzer": "stem_analyzer", "text": "κουρευτικές μηχανές"}'
    {
      "tokens" : [ {
        "token" : "κουρευτ",
        "start_offset" : 0,
        "end_offset" : 11,
        "type" : "<ALPHANUM>",
        "position" : 1
      }, {
        "token" : "μηχαν",
        "start_offset" : 12,
        "end_offset" : 19,
        "type" : "<ALPHANUM>",
        "position" : 2
      } ]
    }

    $ curl -XGET 'http://localhost:9200/test_stemmer/_analyze?pretty' -H 'Content-Type: application/json' -d '{"analyzer": "stem_analyzer", "text": "κουρευτική μηχανή"}'
    {
      "tokens" : [ {
        "token" : "κουρευτ",
        "start_offset" : 0,
        "end_offset" : 10,
        "type" : "<ALPHANUM>",
        "position" : 1
      }, {
        "token" : "μηχαν",
        "start_offset" : 11,
        "end_offset" : 17,
        "type" : "<ALPHANUM>",
        "position" : 2
      } ]
    }

    # Delete test index
    $ curl -XDELETE 'http://localhost:9200/test_stemmer'
    {"ok":true,"acknowledged":true}

### YML configuration example

	index:
	  analysis:
	    filter:
	      stem_greek:
	        type: skroutz_stem_greek


Warning
-------

Input is expected to to be casefolded for Greek (including folding of final
sigma to sigma), and with diacritics removed. This can be achieved with
GreekLowerCaseFilter.

References
----------

* [Development of a Stemmer for the Greek Language](http://people.dsv.su.se/~hercules/papers/Ntais_greek_stemmer_thesis_final.pdf)


Issues
--------

For stemming issues: [here](https://github.com/skroutz/greek_stemmer)
# elasticsearch-skroutz-greekstemmer
