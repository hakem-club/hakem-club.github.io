---
title: Data Model
nav_order: 2
---

# Data Model
{: .no_toc }

- TOC
{:toc}

---

## `PlayingCard` and `PlayingCardSuite`

We need to represent playing cards in some way.
We do this by assigning each one of the 52 playing cards a number between 0 to 51 — starting with 2♥ all the way to A♣

```js
const SUITES = ['♥', '♠', '♦', '♣'];
const RANKS = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A'];
```

> **NOTE**: We keep the ranks in order of their scoring precedence (specifically keeping A in the end rather than beginning because in this game A has the highest winning value)

A playing card is represented like this:

```js
type PlayingCard = 0..51;
```

Similarly a playing card suite (خال), is represented using numbers 0..3 and it's rank using numbers in the 0..12 range:

```js
type PlayingCardSuite = 0..3;
type PlayingCardRank = 0..12;
```

Given a `PlayingCard`, you can workout what is the "suite" and "rank" of the card by using the following simple mathematical functions:


```js
function getSuiteOfCard(card: PlayingCard): PlayingCardSuite {
  return Math.floor(Math.floor(card/13));
}

function getRankOfCard(card: PlayingCard): PlayingCardRank {
  
  return card % 13; // e.g. if card is 2, it means '♥4' or if card is 13, it means '♠2'
}
```

In order to produce a display value for a card number (0-52) we can simply look up the suite and rank in the list of SUITES and RANKS mentioned above and concatenate the results.

```js
function getCardDisplayValue(card: PlayingCard): string {
  const suite = SUITES[getSuiteOfCard(card)];
  const rank = RANKS[getRankOfCard(card)];
  return `${rank}${suite}`
}
```

## `PlayerIndex` and `TeamIndex`

Each game of courpiece, has 4 players. Therefore we represent them with a number in 0..3 range:

```js
type PlayerIndex = 0..3;
```

Similarly there are two teams in a game:

```js
type TeamIndex = 0..1;
```

Players `0` and `2` are in "Team A" and players `1` and `3` are in "Team B". In order to determine the team that player is part of, you can use this simple mathematical funciton:


```js
function getPlayerTeamIndex(player: PlayerIndex): TeamIndex {
  return player % 2;
}
```

## `Trick`

A "trick" is a completed round of 4 cards played in a game of courtpiece.

```js
type TTrick = {
  cards: PlayingCard[]; // exactly 4
  starting_player: PlayerIndex,
  winner: PlayerIndex;
};
```
