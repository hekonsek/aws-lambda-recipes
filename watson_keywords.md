# Getting text keywords using IBM Watson service

## Problem definition

I would like to understand what are keywords associated with my text data. For example keywords in the following text...

    Brazilian Jiu-Jitsu is a martial art, combat sport system 
    that focuses on grappling and especially ground fighting.
    
...should be something like `Brazilian Jiu-Jitsu`, `martial art` and `ground fighting`.

## Solution

[IBM Watson Natural Language Understanding service](https://www.ibm.com/watson/services/natural-language-understanding) provides 
REST API which can be used to get insights from textual data. Such insights include keywords, emotions, named entities and many more. Serverless service 
providing connectivity with Watson Natural Language Understanding is `hekonsek/serverless-lambda-watson-nlu` ([https://github.com/hekonsek/serverless-lambda-watson-nlu](https://github.com/hekonsek/serverless-lambda-watson-nlu)).
    
Event payload format used by Watson NLU function is...

    { "watsonCommand": {...} }
    
...where `watsonCommand` is API request in the format of IBM Watson REST API. We would like to collect insights about submitted text 
using language feature called `keywords`. So final event we should sent to the function should look like the following snippet:

    { "watsonCommand":
      { "text": "I enjoy brazillian jiu-jitsu.",
        "language": "en", "features": {"keywords":{}}
      }
    }


## Example

Deploy `hekonsek/serverless-lambda-watson-nlu` service into AWS using the following code:

    git clone https://github.com/hekonsek/serverless-lambda-watson-nlu
    cd serverless-lambda-watson-nlu
    make
    WATSON_USERNAME=myUserName WATSON_PASSWORD=myPassword serverless deploy

Where `WATSON_USERNAME` and `WATSON_PASSWORD` are Watson credentials associated with your IBM Bluemix account. The default name of the 
function is `watson-nlu-dev-analyze`.

To analyze text keywords, execute the `watson-nlu-dev-analyze` function and send appropriate event using AWS cli tool: 

    $ aws lambda invoke --function-name watson-nlu-dev-analyze --payload \
    '{"watsonCommand": {"text": "I enjoy brazillian jiu-jitsu.", "language": "en", "features": {"keywords":{}}}}' \
    /tmp/output.txt
    
You can see the results of the execution by opening the `/tmp/output.txt` file:

    $ cat /tmp/output.txt
    { "keywords": [{"relevance": 0.987782, "text": "brazillian jiu-jitsu"}],
    "usage": {"text_characters": 29, "features": 1, "text_units": 1}, "language": "en"
    }