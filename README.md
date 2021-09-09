# PunkScape Hackathon #1

![Hackathon #1](https://user-images.githubusercontent.com/2725836/132677018-ed879bec-2169-462e-a03a-e99b72a7b37a.png)

**Topic:** Open Source Package for easy Rarity Score Calculation

**Reward Pool:** 3 ETH. <br>
<small>
> The winning project will be paid 2 ETH. <br>
0.7 ETH goes to the submission voted for 2nd place. <br>
0.3 ETH goes to the submission voted for the 3rd place. <br>
All other participants in the first PunkScapes Hackathon will have savored the joy of being part of it!
</small>

**Deadline:** 26th of September

## Intro

We need an interface for rarity score calculation... The easy way would be to spend ETH with platforms like "Rarity Tools" or "Rarity Sniper" and have them build it for us.

- I built a basic rarity tool for another project and it was just a few hours of programming. I know that with 2-4 days/evenings of dedicated work someone can build something really cool and reusable.
- So instead of paying these projects 2-3 ETH i would like to invest that into our community to develop an open source tool we can share with the NFT community at large...

### The Race Is On...

- If you are a single developer/designer or a team of buddies who design & develop, then this is for you!
- The first PunkScapes Hackathon will be launched with the goal of developing general purpose apps that
  1. take in a collection of ERC721-NFT metadata, calculate the rarity for each trait / attribute as well as each item in the collection & makes that available via a simple metadata API and
  2. makes the data easily queried / viewed in a web interface or via a Discord bot (or both 😅) and
  3. has easy instructions for others to add their collection metadata, install the app on a simple server or platform (like Heroku / DigitalOcean / ...)
- Deadline for the submission of completed projects is 27.09.2021 00:00 UTC.
- The developed submissions are to be open sourced under the MIT license. Of course, you will forever be the authors of these projects but i think it would be very cool branding wise to make all of them available within a PunkScape Github Organisation.
- All submissions will be put up for vote by the community as to which is best. Every holder of a OneDayPunk will have one vote.
- Please choose a common programming language/environment like NodeJS or Python.

If this one goes well i can envision many more of these in the future!

As a good example of an easy to install and configure package check out this open source discord bot for OpenSea sales: https://github.com/0xEssential/opensea-discord-bot

## Requirements:

1. Only OneDayPunk owners may participate in the hackathon
2. The app has to calculate a rarity score for each item in the given collection
3. The app has to be deployed on a live server (Heroku free tier or similar is fine)
4. The app has to be usable: Minimum requirement is that the rarity of each item in the collection can be queried / viewed
5. There must be easy to follow documentation for other developers on how to configure their own collection metadata and deploy it to a server.
6. You have to hold a OneDayPunk to take part in the hackathon. They are currently available for ~50$ on [OpenSea](https://opensea.io/collection/onedaypunks). If you would like to take part but economically can't afford to right now, please reach out to jalil#1001 on discord and we'll figure something out.

## Technical Details

- NFT collections have a metadata `JSON` file for each NFT in the collection, numbered by their `tokenID`. Examples (note the incrementing `tokenID` in the URL): 
  - [OneDayPunk #0](https://ipfs.io/ipfs/QmVtbahSw69pScLgwGUMTnVPR6FkVMeH5ntQimkn5bSD6y/0/metadata.json)
  - [OneDayPunk #1](https://ipfs.io/ipfs/QmVtbahSw69pScLgwGUMTnVPR6FkVMeH5ntQimkn5bSD6y/1/metadata.json)
  - [OneDayPunk #2](https://ipfs.io/ipfs/QmVtbahSw69pScLgwGUMTnVPR6FkVMeH5ntQimkn5bSD6y/2/metadata.json)
  - ...
  - [OneDayPunk #9999](https://ipfs.io/ipfs/QmVtbahSw69pScLgwGUMTnVPR6FkVMeH5ntQimkn5bSD6y/9999/metadata.json)
- Most follow the extended [Metadata Standard defined by OpenSea](https://docs.opensea.io/docs/metadata-standards)

The rarity of each property is calculated as follows:

```
e.g. 1: Property "Type", Attribute "Ape" 
24 of 10000 have this attribute/property combination
rarity(type) = 1/(24/10000) = 416.67

e.g. 2: Property "Nose", Attribute "What Nose?"
212 of 10000 have this attribute/property combination
rarity(nose) = 1/(212/10000) = 47.17

e.g. 3: Property "Nose", Attribute "" (not set)
9788 of 10000 have this attribute/property combination
rarity(nose) = 1/(9788/10000) = 1.02
```

### Tasks:

#### Data Crunching
- [ ] The app lets the deployer configure a collection json file like this one: [collection.json](example-data/collection.json)
- [ ] Take all metadata items and analyse their `attributes`.
- [ ] Derive a rarity score for each attribute of each NFT item. Example: Property "Type", Attribute "Ape"; 24 of 10000 have this attribute/property combination; `rarity_score = 1/(24/10000) = 416.67`
- [ ] Derive a rarity score for each NFT in the collection. The rarity score for each NFT is the **sum of all attributes**.
- [ ] Account for missing `trait_type`s of an NFT.
- [ ] Account for the `trait_count` of an NFT.
- [ ] Calculate the Rarity Score for each token (SUM of all rare trait attributes / missing traits / trait counts).
- [ ] Compute and store a `collection-rarities.json` file which can be exported for use elsewhere for the given collection.

All of this can be done on a per collection basis and data can be stored in memory & exposed via a simple API that spits out the metadata + rarity score data for each token. I imagine something like this (doesn't have to be exactly this structure - just an example...):

```js
// GET /api/0
{
    "id": 0,
    "name": "One Day I'll Be A Punk #0",
    "image": "ipfs://QmUZMuhGPFqAe1LiCxg9qZwNSXtLCYCi5D4aasYH5uiU9a",
    "attributes": [
        {
            "trait_type": "type",
            "value": "Human",
            "percentile": "0.9879",
            "rarity_score": "1.0122482033"
        },
        {
            // ...
        },
    ],
    "missing_traits": [
      {
        "trait_type": "glasses",
        "percentile": "0.123",
        "rarity_score": "123.45"
      }
    ],
    "trait_count": {
      "count": 4,
      "percentile": "0.45",
      "rarity_score": "2.0123"
    },
    "rarity_score": "129.59", // (The sum of all `attribute` rarity scores + missing trait rarity scores)
},
```

**Note about the "missing traits"**: As an example, in the OneDayPunks collection there are tokens that don't have hair. Or don't have a beard. In terms of rarity this is relevant (if most other items do have hair, then not having hair at all makes you rarer than others).

#### Application
The above should be 2-4 hours of simple coding.
After that, let your creativity flow to make this a nice little application. Either build a simple discord bot or web interface to check out the rarities of each item in the collection.

### Note:
As an example, the OneDayPunk Collection has a small bug where tokens with only two traits have an extra, empty trait definition item `{}` (e.g.: token ID `281`). The application has to allow for these little inconsistensies / quirks and must still compute the rarity correctly.

Test your app against different metadata collections. You can download for example the BoredApeYachtClub metadata collection from here: https://ipfs.io/ipfs/QmeSjSinHpPnmXmspMjwiXyN6zS4E9zccariGR3jxcaWtq/1 (you can download the entire folder with IPFS Desktop)...
