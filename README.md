# How to Scrape Studapart: Student Housing France Rents

> **Run it now, no code: [Studapart Scraper on the Apify Store](https://apify.com/bibian/studapart-housing-scraper)** &nbsp;|&nbsp; $2.00 per 1,000 listings &nbsp;|&nbsp; no browser, no proxy

Studapart is a major French student housing platform: studios, shared flats and student residences listed city by city across France. It has no public export for third parties. This guide shows how to get these listings as clean structured data: monthly rent, deposit, surface, furnishing, availability and amenities, with a free Apify Actor, from the console or from code.

Searches this page answers: Studapart scraper, student housing France data, student rent scraper, French housing listings API.

## What you get from the Studapart scraper

- Every current listing in the cities you choose: studios, shared flats and student residences.
- Monthly rent, deposit, surface, room counts, furnishing and amenities (washing machine, parking, pets allowed, and more).
- Availability dates and minimum stay.
- A direct link to the full listing on Studapart.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five results) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | Listing id and public link to the full ad |
| `title`, `propertyType`, `lessorType` | Listing title, property type and whether it is listed by an owner, agency or residence |
| `city`, `zipcode`, `latitude`, `longitude` | Location (approximate position for private owners) |
| `monthlyRent`, `monthlyRentWholeAccommodation`, `deposit` | Rent for one room and for the whole flat, and deposit |
| `surfaceM2`, `roomsCount`, `bedroomsCount`, `maxTenants` | Size and capacity |
| `furnished`, `privateBathroom`, `washingMachine`, `petsAllowed`, `parking` | Amenities |
| `availableFrom`, `availableUntil`, `minStayMonths` | Availability window and minimum stay |
| `description` | Advertiser's description as plain text, with e-mail addresses and phone numbers removed |

## Use cases

- **Track student rents.** Monthly rent, deposit and surface by city and district.
- **Monitor availability.** Which units open up in the coming weeks, and for how long (minimum stay).
- **Map the supply.** Residences and agencies that list in a given city, with their amenities.
- **Feed a housing comparison or alert tool.** Run it daily and compare with the previous dataset.

## Input

| Field | What it does |
|---|---|
| `cities` | One or more French cities, for example `Paris`, `Lyon`, `Toulouse`. Paris, Lyon and Marseille cover all their districts (default `["Paris"]`). |
| `minRent` / `maxRent` | Keep only listings in this monthly rent range (charges included). |
| `availableWithinDays` | Keep only listings that can be rented within this many days. `0` returns every listing the site still shows (default 90). |
| `maxItems` | The run stops once this many listings are saved (default 50). |
| `includeDescriptions` | Add the advertiser's description as plain text, e-mails and phone numbers removed (default true). |

Example input:

```json
{
  "cities": ["Paris", "Lyon"],
  "maxRent": 900,
  "availableWithinDays": 90,
  "maxItems": 50,
  "includeDescriptions": true
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/studapart-housing-scraper) and click **Try for free** (a free Apify account is enough).
2. Fill in the cities and an optional rent range, then click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For a daily availability alert, open **Schedules**, create a daily schedule for the Actor and, optionally, add an integration that sends new results to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/studapart-housing-scraper').call({
    "cities": ["Paris", "Lyon"],
    "maxRent": 900,
    "availableWithinDays": 90,
    "maxItems": 50,
    "includeDescriptions": true
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.city} | ${item.monthlyRent} EUR/month | ${item.surfaceM2} m2`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/studapart-housing-scraper").call(run_input={
    "cities": ["Paris", "Lyon"],
    "maxRent": 900,
    "availableWithinDays": 90,
    "maxItems": 50,
    "includeDescriptions": True,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('city')} | {item.get('monthlyRent')} EUR/month")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$2.00 per 1,000 listings** (that is $0.002 per listing), plus a tiny start fee of $0.00005 per run. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/studapart-housing-scraper) when this guide was written; the Store page is the reference.

## Limits and honest notes

- The site returns at most about 200 listings per search. To collect more, search several cities, narrow the rent range, or run it regularly.
- Cities are matched by name. An unknown name gives no result and a warning in the log.
- For a shared flat, `monthlyRent` is the price of one room and `monthlyRentWholeAccommodation` the price of the whole flat.
- This is an unofficial tool, not affiliated with Studapart. Check the site terms that apply to your use.

## FAQ

**Does it need a browser or a proxy?**  
No. The Actor reads Studapart's own listing and property pages directly, so runs are fast and cheap.

**Does it collect landlord or tenant personal data?**  
No. `lessorType` is `owner`, `agency` or `residence`; only residences and agencies are named. Private landlords are never named and their street address is not collected, only the postal code, city and an approximate position. E-mail addresses and phone numbers typed into descriptions are removed.

**Can I filter by rent or availability?**  
Yes, with `minRent`, `maxRent` and `availableWithinDays`.

**Can I get the data in Excel or Google Sheets?**  
Yes. Export the dataset as CSV, Excel or JSON from the Apify console, or connect the Apify Google Sheets integration.

## Try it

**[Open Studapart Scraper on the Apify Store](https://apify.com/bibian/studapart-housing-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
