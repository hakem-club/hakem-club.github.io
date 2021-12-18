---
title: Data Model
nav_order: 2
---

# Data Model
{: .no_toc }

- TOC
{:toc}

---

## `PlayingCard`

We need to represent playing cards in some way.
We do this by assigning each one of the 52 playing cards a number between 0 to 51 — starting with 2♥ all the way to A♣.

This way of representing a card is concise, size-efficient, preserves both the Suite and Rank of the card and do so in the order of winning value.

```js
type PlayingCard = 0..51;
```



### Computed: `PlayingCardSuite` and `PlayingCardRank`

A playing card suite (خال), is represented using numbers 0..3 and it's rank using numbers in the 0..12 range:

```js
type PlayingCardSuite = 0..3;
type PlayingCardRank = 0..12;
```

Each number in these ranges map to what would be the display value of these suites and ranks.

```js
const SUITES = ['♥', '♠', '♦', '♣'];
const RANKS = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A'];
```

> **NOTE**: We keep the ranks in order of their scoring precedence (specifically keeping A in the end rather than beginning because in this game A has the highest winning value)

Given a `PlayingCard` (number), you can workout what is the "suite" and "rank" of the card by using the following simple mathematical functions:


```js
function getSuiteOfCard(card: PlayingCard): PlayingCardSuite {
  return Math.floor(Math.floor(card/13));
}

function getRankOfCard(card: PlayingCard): PlayingCardRank {
  return card % 13;
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

In order to find the team mate of a player:

```js
function getPlayerTeamMate(player: PlayerIndex): PlayerIndex {
  return (player + 2) % 4;
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