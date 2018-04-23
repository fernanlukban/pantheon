# Pantheon

Simple library to use the Riot API with Python Asyncio.

Supports all endpoints except static data and the tournaments ones (and matchs by tournament code).

It has an efficient rate limiting system as well as an error handler that automatically resend request on when needed.

Currently supported requests : 
 * champion masteries by summonerId -> getChampionMasteries(summonerId)
 * champion masteries by summonerId and championId -> getChampionMasteriesByChampionId(summonerId,championId)
 * champion masteries score by summonerId -> getChampionMasteriesScore(summonerId)
 * champions -> getChampions()
 * champions by championId -> getChampionsById(championId)
 * league by leagueId -> getLeagueById(leagueId)
 * league position by summonerId -> getLeaguePosition(summonerId)
 * challenger league by queue (optional) -> getChallengerLeague(queue)
 * lol status -> getStatus()
 * master league by queue (optional) -> getMasterLeague(queue)
 * match by matchId -> getMatch(matchId)
 * match timeline by matchId -> getTimeline(matchId)
 * matchlist by accountId (with parameters) -> getMatchlist(self, accountId, params) / params={"queue":420,"season":11}
 * current game by summonerId -> getCurrentGame(summonerId)
 * summoner by summonerId -> getSummoner(summonerId)
 * summoner by accountId -> getSummonerByAccountId(accountId)
 * summoner by name -> getSummonerByName(name)
 * third party code by summonerId -> getThirdPartyCode(summonerId)

Further requests supported and documentation (hopefully) incoming

To install, download it and run 

```
python setup.py install
```

An example of usage to get details on the last 10 games : 

```
from pantheon import pantheon
import asyncio

server = "euw1"
api_key = "RGAPI-XXXX"

panth = pantheon.Pantheon(server, api_key, True)

async def getSummonerId(name):
    try:
        data = await panth.getSummonerByName(name)
        return (data['id'],data['accountId'])
    except Exception as e:
        print(e)


async def getRecentMatchlist(accountId):
    try:
        data = await panth.getMatchlist(accountId, params={"endIndex":10})
        return data
    except Exception as e:
        print(e)

async def getRecentMatches(accountId):
    try:
        matchlist = await getRecentMatchlist(accountId)
        tasks = [panth.getMatch(match['gameId']) for match in matchlist['matches']]
        return await asyncio.gather(*tasks)
    except Exception as e:
        print(e)


name = "Canisback"

loop = asyncio.get_event_loop()  

(summonerId, accountId) = loop.run_until_complete(getSummonerId(name))
print(summonerId)
print(accountId)
print(loop.run_until_complete(getRecentMatches(accountId)))
```