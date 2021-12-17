---
title: Bots API
nav_order: 2
---

# Bots API

{:toc}

## What is a Bot

We have designed [Hakem.club](http://hakem.club/) in such way that allows you to write a “bot” that participate in games of Courtpiece (حکم).


## How do I write a Bot?

These are the steps:

* Write two HTTP APIs in the language/framework if your choice
    * One for **picking a trump suite** (حکم) — you might become the lead player (حاکم) in a given game which means our platform will need to ask you for this.
    * One for **playing a card** when asked for.
* Deploy your APIs to somewhere publicly available. Examples of how you can deploy your API, some offer largely free hosting.
    * AWS Lambda
    * Netlify Functions
    * Heroku
    * ...
* Contact one of our admins ([@mvalipour](https://twitter.com/mvalipour)) to add your bot to the platform.

## Open Questions

* We need to figure out a way for Bot developers to test their bots.
* We need to figure out a way to authenticate bot API calls
    * either through pre-auth URLs
    * or `access-token` header.

* * *

### API: Choose a trump suite

We will call this API when we need a bot to choose a trump suite (hokm).

URL:

```
POST https://yourserver.com/choose_trump_suite
```

Request Payload (body) 

```js
export type TPayload = {
  // exactly the first 5 cards from your hand
  your_cards: PlayingCard[];
};
```

e.g.

```js
{ 
    "your_cards": [ 27, 48, 21, 4, 44 ]
}
```

Response Payload (body) 

```
// a number from 0-3 (PlayingCardSuite)
2
```

* * *

### API: Play a Card

We will call this API when we need a bot to play a card.

URL:

```
POST https://yourserver.com/play_card
```

Request Payload (body) 

```js
export type TPayload = {
  // index of the lead player (hakem)
  whos_lead: PlayerIndex;

  // index of the player whose requested this data
  your_index: PlayerIndex;

  // current cards in hands of the player whose requested this data
  your_cards: PlayingCard[];

  // current (playable) cards in hands of the player whose requested this data
  your_cards_your_can_play: PlayingCard[];
  
  // cards currently on the floor
  floor_cards: PlayingCard[];

  // trump suite — as chosen by the starting player of this game
  trump_suite: PlayingCardSuite;

  // wins per team — array of length two indicating number of wins by each team
  wins: [number, number];

  // list of all tricks (previous rounds) played
  tricks: TTrick[],
};

export type TTrick = {
  cards: PlayingCard[]; // exactly 4
  starting_player: PlayerIndex,
  winner: PlayerIndex;
};
```

e.g:

```js
{
  "whos_lead": 2,
  "your_index": 3,
  "your_cards": [45, 24, 43, 30, 38, 20, 21, 26, 39, 16, 19, 28],
  "your_cards_your_can_play": [24, 20, 21, 16, 19],
  "floor_cards": [13],
  "trump_suite": 2,
  "wins": [1, 0],
  "tricks": [
    {
      "cards": [25, 22, 14, 15],
      "starting_player": 2,
      "winner": 2
    }
  ]
}
```

Response Payload (body) 

```
// a number from 0-51 (PlayingCard)
// one of the cards from `your_cards_your_can_play`
21
```

* * *

## Random Bot

https://github.com/hakem-club/courtpiece-bot-random/blob/master/api/bots/random.ts

## High-card Bot

https://github.com/hakem-club/courtpiece-bot-random/blob/master/api/bots/highcard.ts


e.g. — play_card on random bot

```
curl -X POST \
  https://hakemclub-bot-random.herokuapp.com/bots/random-bot/play_card \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 2d375eb5-3f60-28f5-cdcc-50c08307cff5' \
  -d '{
  "whos_lead": 2,
  "your_index": 3,
  "your_cards": [45, 24, 43, 30, 38, 20, 21, 26, 39, 16, 19, 28],
  "your_cards_your_can_play": [24, 20, 21, 16, 19],
  "floor_cards": [13],
  "trump_suite": 2,
  "wins": [1, 0],
  "tricks": [
    {
      "cards": [25, 22, 14, 15],
      "starting_player": 2,
      "winner": 2
    }
  ]
}'
```

```
20
```


e.g. — choose_trump_suite on high-card bot

```
curl -X POST \
  https://hakemclub-bot-random.herokuapp.com/bots/highcard-bot/choose_trump_suite \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 2d375eb5-3f60-28f5-cdcc-50c08307cff5' \
  -d '{ 
    "your_cards": [ 27, 48, 21, 4, 44 ]
}'
```

```
3
```



