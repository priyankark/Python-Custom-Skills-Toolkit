# Azure Cognitive Search Python Custom Skill For Dates Extraction

This code is a Python Custom Skill, for Azure Cognitive Search, based on Azure Functions for Python. It extracts the first date from the input string. If you need all of the dates, or the time, change the code as you need.

The Built-in [Entity Recognition cognitive skill](https://docs.microsoft.com/en-us/azure/search/cognitive-search-skill-entity-recognition) for dates will return all dates of the document, in multiple formats, as you can see in the image below. If it is not a problem for you, you don't need to use this custom skill.

![Dates](../images/dates.JPG)

## Required steps

1. Follow [this](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-python) tutorial.
1. Use the Python code below as your **__init__.py** file. Customize it with your storage account details, also with your csv file name and target column. As you can see below, my sample csv file target column name is **Term**. That helps the idea that this code will extract pre-defined terms from the documents content.
1. Don't forget to add **azure.functions** and **datefinder** to your requirements.txt file.
1. Connect your published custom skill to your Cognitive Search Enrichment Pipeline. Plesae check the section below the code in this file. For more information, click [here](https://docs.microsoft.com/en-us/azure/search/cognitive-search-create-custom-skill-example#connect-to-your-pipeline).

## Python Code

The Python code for this skill is [here](./__init__.py). 

## Add this skill to your Cogntive Search Enrichment Pipeline

Your skillset will have this extra section below.

```json
 {
            "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
            "name": "First Date",
            "description": "Get the first date detected in a string",
            "context": "/document",
            "uri": "your-Pyhton-Azure-Functions-published-URL",
            "httpMethod": "POST",
            "timeout": "PT30S",
            "batchSize": 1,
            "degreeOfParallelism": null,
            "inputs": [
             {
               "name": "text",
               "source": "/document/content"
             }
                   ],
        "outputs": [
          {
            "name": "text",
            "targetName": "date"
          }
            ],
            "httpHeaders": {}
           }
```

## Sample Input

Use the JSON input below to test your function. Get familiar with the code behavior in the different situations. 

The test is a tribute to the most popular football club in the world, [Flamengo](https://en.wikipedia.org/wiki/Clube_de_Regatas_do_Flamengo), from Rio de Janeiro. It was founded in 1895 and has over 45 million fans in Brazil alone. The team was [champion](https://www.youtube.com/watch?time_continue=11&v=371FOyquzno) in its two most important matches of 2019, the Brazilian championship and the Copa Libertadores of America.

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
            "text": ["Flamengo was founded on November 15th 1895"]
           }
      } ,
        {
        "recordId": "1",
        "data":
           {
            "text": [""]
           }
      } ,    
      {
        "recordId": "2",
        "data":
           {
            "text": ["Flamengo campeão de tudo em 2019!"]
           }
      } 
    ]
}

```

## Expected Output

```json
{
    "values": [{
        "recordId": "0",
        "data": {
            "text": "1895-11-15"
        }
    }, {
        "recordId": "1",
        "errors": [{
            "message": "Could not complete operation for record."
        }]
    }, {
        "recordId": "2",
        "data": {
            "text": "2019-01-31"
        }
    }]
}
```

