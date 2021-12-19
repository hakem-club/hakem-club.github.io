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

We need to be able to store and pass-around a notion of playing cards in some way.

Here we do this by assigning each one of the 52 playing cards a number between 0 to 51 — starting with 2♥ all the way to A♣.

This way of representing a card is concise, size-efficient, preserves both the Suite and Rank of the card and do so in the order of winning value.

When we refer to a `PlayingCard`, we are referring to a `number` in this range (`0..51`).

```js
type PlayingCard = 0..51;
```

But how are the 52 cards apread across this range?

- The first 13 cards are "hearts" Similarly the next 13 are "spades", 13 after that "diamonds" and the final 13 "clubs"
- In each set of 13 cards in this range, the order is based on their "winning value" — i.e. 2 comes first, followed by 3, 4, ... all the way to the Ace.

> **NOTE**: We keep the ranks in order of their "winning value" (specifically keeping A in the end rather than beginning because in this game A has the highest winning value)

![image](https://user-images.githubusercontent.com/2564173/146656706-f2f6512b-eb36-42c7-b398-3950faa1d4a3.png)

As you can see from this figure, spreading the cards into this range in this fashion, allows us to very easily figure out what "suite" and what "rank" each card is.

In the section below, we will explain more how this is implemented.

---


## `PlayingCardSuite` and `PlayingCardRank`

Generally in a 52-card-deck of playing cards, we have the following "Suites" (خال) and "Ranks":

```js
const SUITES = ['♥', '♠', '♦', '♣'];
const RANKS = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A'];
```

When we refer to a `PlayingCardSuite` we are referring to an index — a `number` in the `0..3` range — and similarly for `PlayingCardRank` — a `number` in the `0..12` range.

```js
type PlayingCardSuite = 0..3;
type PlayingCardRank = 0..12;
```

### Calculating the "Suite" and "Rank" of a `PlayingCard`

Given a `PlayingCard` (number), you can workout what is the "suite" and "rank" of the card by using the following simple mathematical functions:

```js
function getSuiteOfCard(card: PlayingCard): PlayingCardSuite {
  return Math.floor(Math.floor(card/13));
}

function getRankOfCard(card: PlayingCard): PlayingCardRank {
  return card % 13;
}
```

e.g. (as seen on the figure above) — number 14 represents "3 of Spades" because:

- `14 divide by 13` equals `1` — which refers to "Spades" Suite
- `14 modulo 13` equals `1` — which refers to "3" Rank

Another example: number 49 represents "Queen of Clubs" because:

- `49 divide by 13` equals `3` — which refers to "Clubs" Suite
- `49 modulo 13` equals `10` — which refers to "Q" Rank


In order to produce a display value for a card number (0-52) we can simply look up the suite and rank in the list of SUITES and RANKS mentioned above and concatenate the results.

```js
function getCardDisplayValue(card: PlayingCard): string {
  const suite = SUITES[getSuiteOfCard(card)];
  const rank = RANKS[getRankOfCard(card)];
  return `${rank}${suite}`
}
```

---

## `PlayerIndex` and `TeamIndex`

Each game of courpiece is played between 4 players in 2 teams.

Therefore when we refer to `PlayerIndex` we are referring to an index — a `number` in the `0..3` range — and similarly for `TeamIndex` — a `number` in the `0..1` range.

```js
type PlayerIndex = 0..3;
type TeamIndex = 0..1;
```

### Calculating the "Team" and "Team Mate" of a `PlayerIndex`

Players `0` and `2` are in "Team A" and players `1` and `3` are in "Team B". In order to determine the team that a given player is part of, you can use this simple mathematical funciton:

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

- `starting_player` — refers to the player who started the round.
- `cards` — is the list of cards played in a round — in the order of play — i.e. first card in this list is played by `starting_player`, second one player after that, etc.
  - e.g. if `starting_player` is set to `2` — cards in the round are played by players 2, 3, 0 and 1 respectively.
- `winner`: refers to the player who won the round.

