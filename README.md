
# Exploring and Transforming JSON Schemas

## Introduction

In this lesson, you'll formalize your knowledge for how to explore a JSON file whose structure and schema is unknown to you. This often happens in practice when you are handed a file or stumble upon one with little documentation.

## Objectives
You will be able to:

* Use the JSON module to load and parse JSON documents
* Load and explore unknown JSON schemas
* Convert JSON to a pandas dataframe


## Loading the JSON file

As before, you'll begin by importing the `json` package, opening a file with python's built-in function, and then loading that data in.


```python
import json
```


```python
f = open('output.json')
data = json.load(f)
```

## Exploring JSON Schemas  

Recall that JSON files have a nested structure. The most granular level of raw data will be individual numbers (float/int) and strings. These, in turn, will be stored in the equivalent of python lists and dictionaries. Because these can be combined, you'll start exploring by checking the type of our root object and start mapping out the hierarchy of the JSON file.


```python
type(data)
```




    dict



As you can see, in this case, the first level of the hierarchy is a dictionary. Let's explore what keys are within this:


```python
data.keys()
```




    dict_keys(['albums'])



In this case, there is only a single key, 'albums', so you'll continue on down the pathway exploring and mapping out the hierarchy. Once again, start by checking the type of this nested data structure.


```python
type(data['albums'])
```




    dict



Another dictionary! So thus far, you have a dictionary within a dictionary. Once again, investigate what's within this dictionary (JSON calls the equivalent of Python dictionaries Objects.)


```python
data['albums'].keys()
```




    dict_keys(['href', 'items', 'limit', 'next', 'offset', 'previous', 'total'])



At this point, things are starting to look something like this: 
<img src="images/json_diagram1.JPG" width=550>

At this point, if you were to continue checking individual data types, you have a lot to go through. To simplify this, you can use a for loop:


```python
for key in data['albums'].keys():
    print(key, type(data['albums'][key]))
```

    href <class 'str'>
    items <class 'list'>
    limit <class 'int'>
    next <class 'str'>
    offset <class 'int'>
    previous <class 'NoneType'>
    total <class 'int'>


Adding this to our diagram we now have something like this:
<img src="images/json_diagram2.JPG" width=550>

Normally, you may not draw out the full diagram as done here, but it's a useful picture to have in mind, and in complex schemas, can be useful to map out. At this point, you also probably have a good idea of the general structure of the JSON file. However, there is still the list of items, which we could investigate further:


```python
type(data['albums']['items'])
```




    list




```python
len(data['albums']['items'])
```




    2




```python
type(data['albums']['items'][0])
```




    dict




```python
data['albums']['items'][0].keys()
```




    dict_keys(['album_type', 'artists', 'available_markets', 'external_urls', 'href', 'id', 'images', 'name', 'type', 'uri'])



## Converting JSON to Alternative Data Formats
As you can see, the nested structure continues on: our list of items is only 2 long, but each item is a dictionary with a large number of key-value pairs. To add context, this is actually the data that you're probably after from this file: its that data providing details about what albums were recently released. The entirety of the JSON file itself is an example response from the Spotify API (more on that soon). So while the larger JSON provides us with many details about the response itself, our primary interest may simply be the list of dictionaries within data -> albums -> items. Preview this and see if you can transform it into our usual Pandas DataFrame.


```python
import pandas as pd
```

On first attempt, you might be tempted to pass the whole object to Pandas. Try and think about what you would like the resulting dataframe to look like based on the schema we are mapping out. What would the column names be? What would the rows represent?


```python
df = pd.DataFrame(data['albums']['items'])
df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>album_type</th>
      <th>artists</th>
      <th>available_markets</th>
      <th>external_urls</th>
      <th>href</th>
      <th>id</th>
      <th>images</th>
      <th>name</th>
      <th>type</th>
      <th>uri</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>single</td>
      <td>[{'external_urls': {'spotify': 'https://open.s...</td>
      <td>[AD, AR, AT, AU, BE, BG, BO, BR, CA, CH, CL, C...</td>
      <td>{'spotify': 'https://open.spotify.com/album/5Z...</td>
      <td>https://api.spotify.com/v1/albums/5ZX4m5aVSmWQ...</td>
      <td>5ZX4m5aVSmWQ5iHAPQpT71</td>
      <td>[{'height': 640, 'url': 'https://i.scdn.co/ima...</td>
      <td>Runnin'</td>
      <td>album</td>
      <td>spotify:album:5ZX4m5aVSmWQ5iHAPQpT71</td>
    </tr>
    <tr>
      <th>1</th>
      <td>single</td>
      <td>[{'external_urls': {'spotify': 'https://open.s...</td>
      <td>[AD, AR, AT, AU, BE, BG, BO, BR, CH, CL, CO, C...</td>
      <td>{'spotify': 'https://open.spotify.com/album/0g...</td>
      <td>https://api.spotify.com/v1/albums/0geTzdk2Inlq...</td>
      <td>0geTzdk2InlqIoB16fW9Nd</td>
      <td>[{'height': 640, 'url': 'https://i.scdn.co/ima...</td>
      <td>Sneakin’</td>
      <td>album</td>
      <td>spotify:album:0geTzdk2InlqIoB16fW9Nd</td>
    </tr>
  </tbody>
</table>
</div>



Not bad, although you can see some of our cells still have nested data within them. The artists column in particular might be nice to break apart. You could do this from the original json, but at this point, let's work with our DataFrame. Preview an entry.


```python
df.artists.iloc[0]
```




    [{'external_urls': {'spotify': 'https://open.spotify.com/artist/2RdwBSPQiwcmiDo9kixcl8'},
      'href': 'https://api.spotify.com/v1/artists/2RdwBSPQiwcmiDo9kixcl8',
      'id': '2RdwBSPQiwcmiDo9kixcl8',
      'name': 'Pharrell Williams',
      'type': 'artist',
      'uri': 'spotify:artist:2RdwBSPQiwcmiDo9kixcl8'}]



As you can see, you have a list of dictionaries, in this case with only one entry as theirs only one artist. You can imagine wanting to transform this for an artist1, artist2,...columns. This will be a great exercise in the upcoming lab to practice your Pandas skills and lambda functions!

## Summary

JSON files often have a deep, nested structure that can require initial investigation into the schema hierarchy in order to become familiar with how data is stored. Once done, it is important to identify what data you are looking to extract and then develop a strategy to transform it into your standard workflow (which generally will be dependent on Pandas DataFrames or NumPy arrays). 

In this lesson, you've seen how to load JSON files using the json module, how to explore these files to get to know their schema, and how to convert a JSON file to a pandas DataFrame.
