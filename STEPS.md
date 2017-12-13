This command to clone the repository into the directory `campaigns`

```
user@user:~/invi.sible.link/campaigns$ git clone git@github.com:vecna/gobwebsecpy
Cloning into 'gobwebsecpy'...
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

With the command `queueCampaign` command to import the CSV content into a list of tasks, they are named `promises` and they will be absolved by an agent. Normally The agent runs in different computers than the control system, in this case, by default, they works on `localhost`
```
user@user:~/invi.sible.link$ DEBUG=* bin/queueCampaign.js --csv campaigns/gobwebsecpy/lista_paraguay.csv --campaign gobwebsecpy
  lib:queue importSiteFromCSV: 7 entries in campaigns/gobwebsecpy/lista_paraguay.csv +0ms
  lib:queue importSiteFromCSV imported 6 sites +4ms
  lib:mongo read in promises2 by {"id":"ebc0877782542dc79ab68d87fbc0897f06cba58c"} sort by {} got 0 results +194ms
  lib:mongo read in promises2 by {"id":"7693e120989b9a71f3e1cdc64fa3c40c836c9c35"} sort by {} got 0 results +14ms
  lib:mongo read in promises2 by {"id":"7b8834daf428efecbd4e0697bec603d79bf25552"} sort by {} got 0 results +4ms
  lib:mongo read in promises2 by {"id":"49c107748cf557751a053e35770fcd1d0e21becb"} sort by {} got 0 results +3ms
  lib:mongo read in promises2 by {"id":"ba3c564e9b83af91af360aef0faacc6f976b0c7f"} sort by {} got 0 results +4ms
  lib:mongo read in promises2 by {"id":"529f3fb6ac4269a569c88c0367c77fe82733aea5"} sort by {} got 0 results +13ms
  lib:mongo read in promises2 by {"id":"3b4e1beed28bed1d12df335420b048e05d4cc0d6"} sort by {} got 0 results +13ms
  lib:mongo read in promises2 by {"id":"003d1cc6e2c55f47dfbe81bdb680ac8dd76add9c"} sort by {} got 0 results +7ms
  lib:mongo read in promises2 by {"id":"b18ac328b4e3e8766d00725bc1db927b31d35d01"} sort by {} got 0 results +24ms
  lib:mongo read in promises2 by {"id":"7b99fb1d61936cd8a962cb1f64047d04527ba07e"} sort by {} got 0 results +18ms
  lib:mongo read in promises2 by {"id":"1131a6ef092a68d971f6bdda18d63c7e812fc0d5"} sort by {} got 0 results +15ms
  lib:mongo read in promises2 by {"id":"869bec677ce995a869abd1413029dd2ddfc4abfa"} sort by {} got 0 results +8ms
  lib:mongo writeMany done: in promises2 12 objects +35ms
  lib:queue added [gobwebsecpy] 12 promises  +1ms
  lib:queue 12 object, a random obj is {
  "kind": "badger",
  "start": "2017-12-13T00:00:00.000Z",
  "id": "7b99fb1d61936cd8a962cb1f64047d04527ba07e",
  "href": "https://www.senatics.gov.py",
  "campaign": "gobwebsecpy",
  "when": "2017-12-13T16:27:00.000Z",
  "description": "",
  "rank": 2,
  "subjectId": "be3d8acee6557e8176caa0ced6be44bb1864ecc2",
  "testId": "557ce24ad5166a0dcde0742d53734d72c664543c",
  "_id": "5a3154d62848d60902f70f5b"
} +0ms
user@user:~/invi.sible.link$ 
```
Note: the campaing name I used is **gobwebsecpy**

This command start a service, it is named **vigile** and serve the `promises` created above:
```
user@user:~/invi.sible.link$ npm run vigile
```

Then I run the agent(s). the first command uses phantomjs, the second seleniumHQ+privacyBadger
```
user@user:~/invi.sible.link$ concurrency=1 amount=6 npm run phantom
user@user:~/invi.sible.link$ concurrency=1 amount=6 npm run badger
```

When there are done, they have saved their evidences. In the server where the agent(s) runs, this service has to run to export the evidences:

```
user@user:~/invi.sible.link$ npm run exposer
```

Now, we launch the two commands which takes the evidences and do an aggregated analysis per site:
```
user@user:~/invi.sible.link$ DEBUG=* bin/analyzePhantom.js --campaign gobwebsecpy --config config/analyzerDevelopment.json 
user@user:~/invi.sible.link$ DEBUG=* bin/analyzeBadger.js --campaign gobwebsecpy --config config/analyzerDevelopment.json 
```

Starting then the service which is intended to export the resuls:

```
user@user:~/invi.sible.link$ npm run storyteller
```

It is possible download the JSON files with the analysis results ad the URL http://localhost:7000/api/v1/mixed/gobwebsecpy

This file is saved in the repository with name `gobwebsecpy-mixed-results.json`, it contains a list of two elements, the first is the result of Phantom analysis (trackers, headers, company attribution), the second on the javascript calls performed by the third party scripts

### Continuity note

I've setup the campaing to provide an updated results (every 24hours) from: https://invi.sible.link/api/v1/mixed/gobwebsecpy (but at the moment I've some stability issue, will be fixed in the next days)
