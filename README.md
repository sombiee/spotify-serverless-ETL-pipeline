# spotify-serverless-ETL-pipeline
This toyproject is done to build a serverless data pipeline to analyse `Top 50 Global` playlist from Spotify: How Top 50 Global songs are changed, which song lasts longer by their properties, etc. Since `Top 50 Global` playlist updates daily, we need daily triggered event to dump data from Spotify API, extract valuable data from dump, transform into structured schema, and load transformed data into analyze pipeline, where SQL is excutable.

## Architecture
![serverless-etl](https://user-images.githubusercontent.com/43290363/224493335-92b25add-441c-4b65-b6bb-11509938b3df.png)


## Used Stack 
- Lanaguage: Python 3.8
- Data Stream: Spotify API (spotipy)
- AWS
    - Cloudwatch, Billing
    - Event Bridge
    - S3
    - Lambda
    - Glue: Crawler, Catalog
    - Athena
    - IAM

## Exploring Spotify Web API using Spotipy
[Spotify for developers](https://developer.spotify.com/) offers official Web APIs to access various data. However, for Python users, [Spotipy](https://spotipy.readthedocs.io/en/2.22.1/) provides an easier ways to acess Spotify Web API using python. Detailed description is listed on [Spotipy](https://spotipy.readthedocs.io/en/2.22.1/). Here, I will highlight on data that is going to be used on the project. All processes are done on Jupyter notebook.


### Access Web API (Spotipy Oauth)
``` python
import os
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials

# client_id, client_secreat code is shown on the Spotify Web API dashboard
client_id = os.environ.get('client_id')
client_secret = os.environ.get('client_secret')

client_credentials_manager = SpotifyClientCredentials(client_id = client_id, client_secret = client_secret)

sp = spotipy.Spotify(client_credentials_manager = client_credentials_manager)

playlist_link = 'https://open.spotify.com/playlist/{url}'
playlist_URI = playlist_link.split('/')[-1]

data = sp.playlist_tracks(playlist_URI)
```

### Fetched data structure (.json)
```yaml
// Data fetched from `sp.playlist_tracks()`
{
    'href': '...(omitted)',
    'item': '{...}'
}
```
`data['href']` refers API endpoints. Detailed data is enlisted on `data['item']`.

### Fetched nested data structure(.json): 'item'
To dig deeper into the fetched data, we need to look deeper into `data['item']`. `data['item']` is consisted as

```yaml
// data['item']
{
    'added_at': '2023-03-10T10:51:45Z',
    'added_by': {...},
    'is_local': False,
    'primary_color': None,
    'track': {...},
    'video_thumbnail': {'url': None} 
}
```
We are not interesed in who/what added songs to the playlist. However, track includes useful data. Sample a song with `data['item'][#]['track']`
``` yaml
// data['item'][#]['track']
{
    'track': {
        'album': {
            'id': '7bnqo1fdJU9nSfXQd3bSMe',
            'name': 'Ditto',
            'release_date': '2022-12-19',
            'total_tracks': '1',
            'external_urls': {'spotify': '...(omitted)/7bnqo1fdJU9nSfXQd3bSMe'},
            ...},
        'artists': [{
            'id': '6HvZYsbFfjnjFrWF950C9d',
            'name': 'NewJeans',
            'external_urls': {'spotify': '...(omitted)/6HvZYsbFfjnjFrWF950C9d'},
            ...}],
        'id': '3r8RuvgbX9s7ammBn07D3W',
        'name': 'Ditto',
        'duration_ms': '185506',
        'external_urls': {'spotify': '...(omitted)'},
        'popularity': '89',
        'added_at': '2023-03-10T10:51:45Z',
        ...
    }
}
```

## (In progress) Serverless to EC2 Server
---
Same ETL pipeline but with Apache Kafka on AWS EC2 👉 [ETL with Kafka server](https://github.com/sombiee/spotify-kafka-pipeline)

![etl-on-server](https://user-images.githubusercontent.com/43290363/224494127-9444539f-82a9-4001-ac26-887f33b5e72c.png)

Currently, I do not have enough server resources to run spark properly. Instead of moving from free-tier ec2 instance, I will use AWS Glue and Athena for data analysis.