---
layout: post
title: fxhash graphql api
---

fxhash exposes a graphql api, more on the topic here:

[Integration guide](https://www.fxhash.xyz/doc/fxhash/integration-guide)

quick and easy client to explore the api:

[API Explorer](https://studio.apollographql.com/sandbox/explorer)

sample python mockup to list the upcoming projects:

```
from gql import gql, Client
from gql.transport.aiohttp import AIOHTTPTransport
import json

# Select your transport with a defined url endpoint
transport = AIOHTTPTransport(url="https://api.fxhash.xyz/graphql")

# Create a GraphQL client using the defined transport
client = Client(transport=transport, fetch_schema_from_transport=True)

# Provide a GraphQL query
query = gql(
    """
    query Query($filters: GenerativeTokenFilter, $take: Int, $sort: GenerativeSortInput) {
        generativeTokens(filters: $filters, take: $take, sort: $sort) {
            id
            mintOpensAt
            pricingFixed {
                price
                opensAt
            }
            slug
            thumbnailUri
            pricingDutchAuction {
                levels
                opensAt
                finalPrice
            }
        }
    }
"""
)

query_vars = {
  "filters": {
    "mintOpened_eq": False
  },
  "take": 20,
  "sort": {
    "mintOpensAt": "ASC"
  },
}

# Execute the query on the transport
result = client.execute(query, variable_values=query_vars)
```

and some extension for above to generate a google calendar url for a notification with some metadata:


```
for item in result['generativeTokens']:
    #print(item['slug'])
    found = False
    if item['pricingFixed'] is not None:
        if item['pricingFixed']['price'] <= 1000000:
            found = True
            print('---------------------------------')
            print('NAME:', item['slug'])
            print('PRICE:', item['pricingFixed'])
            print('OPENS:', item['mintOpensAt'])
            print('IMG: https://gateway.fxhash2.xyz/'+item['thumbnailUri'].replace(':/', ''))
            print('URL: https://www.fxhash.xyz/generative/slug/' + item['slug'])
    if item['pricingDutchAuction'] is not None:
        if (0 in item['pricingDutchAuction']['levels'] or 
            1000000 in item['pricingDutchAuction']['levels']):
            found = True
            print('---------------------------------')
            print('NAME:', item['slug'])
            print('PRICE:', item['pricingDutchAuction'])
            print('OPENS:', item['mintOpensAt'])
            print('IMG: https://gateway.fxhash2.xyz/'+item['thumbnailUri'].replace(':/', ''))
            print('URL: https://www.fxhash.xyz/generative/slug/' + item['slug'])
    if found:
        cal = 'https://calendar.google.com/calendar/render?action=TEMPLATE&'
        text = 'text=fxhash:' + item['slug'] + '&'
        d = item['mintOpensAt'].replace('-','').replace(':','').replace('.','')
        dates = 'dates=' + d + '/' + d + '&'
        details = 'details=' + 'https://www.fxhash.xyz/generative/slug/' + item['slug']
        print('CAL: ' + cal + text + dates + details)
```

the [api explorer](https://studio.apollographql.com/sandbox/explorer) has a very nice intuitive query builder

the fxhash website uses the same api to present the content as well, one can checkout queries by inspecting
the requests in the web browsers dev tools - network console (look for https://api.fxhash.xyz/graphql calls),
more in the integration guide: [integration guide](https://www.fxhash.xyz/doc/fxhash/integration-guide)
