# Skull-king
Skull king Game

GAME OVERVIEW
Skull King is a trick-taking card game played over multiple rounds. Each round, players are dealt a hand of cards (hand size = round number). Players simultaneously bid how many tricks they will win that round, then play tricks. Points are awarded based on bid accuracy and certain bonuses/penalties involving special cards.

CARD TYPES
The deck includes:
1) Suited number cards (four suits). Numbers typically run 1–14.
2) Special cards:
   - Escape (lowest / loses tricks)
   - Pirate (beats all suited cards)
   - Skull King (beats pirates; interacts with Mermaid)
   - Mermaid (special: beats Skull King but loses to pirates; also provides bonus if captured by Skull King depending on official rules)
   - Optional: Treasure/Map/other expansion cards should NOT be included unless enabled via a setting.

TRICK WINNING (core)
A trick is one card from each player in turn order.
- If any Pirate/Skull King/Mermaid is played in the trick, they decide the winner using this priority:
   - Mermaid beats Skull King.
   - Skull King beats Pirates.
   - Pirates beat all suited number cards.
   - If multiple Pirates are played, highest Pirate (first played or equal rank depending on rules) wins — implement as “first Pirate played wins” unless a rank system is explicitly configured.
- Escape cards generally lose the trick (unless all players play Escape; then the first Escape played wins).
- If no special cards are played (Pirate/Skull King/Mermaid), then:
   - The suit led (first non-Escape suited card led) is the suit to follow.
   - Highest number in the led suit wins the trick.
- Following suit:
   - If suit is led, players must play a card of that suit if they have one; otherwise they may play any card (including specials).

BIDDING
At the start of each round:
- Each player bids an integer number of tricks they expect to win.
- Bids are hidden until all have chosen, then revealed.
- Some tables allow a special “0 bid” (winning zero tricks).

SCORING (standard baseline; implement exactly as follows unless a “ruleset” toggle is provided)
For each player:
- If the player makes their bid exactly:
   - Score = +20 × bid
   - If bid is 0 and they win 0 tricks: Score = +10 × round number
- If the player misses their bid:
   - Score = −10 × |(tricks won) − (bid)|
   - If bid is 0 but they win at least 1 trick: Score = −10 × round number
Bonuses:
- Capturing a Pirate with Skull King: +30 points per Pirate captured by Skull King (i.e., Skull King wins a trick that contains one or more Pirates).
- Capturing Mermaid with Skull King: apply official baseline: if Skull King wins a trick containing Mermaid (and Mermaid did not beat SK), award +50. If Mermaid beats Skull King (i.e., Mermaid wins the trick), then no SK bonus applies.
(If there is ambiguity in these bonus interactions, design the code with a configurable rules module and document assumptions in README.)

GAME FLOW
- Support 2–6 players.
- Rounds start at 1 card each and increase by 1 each round until a max round where the deck supports the deal fairly (commonly 10 rounds for 2–6 players). Make this configurable.
- Dealer rotates each round.
- Each trick starts with the player after the dealer (or dealer depending on chosen convention); choose a convention and use it consistently, document it.
- Players play one card per trick until hands are empty.
- Track tricks won per player per round.
- Apply scoring and update total score.
- After final round, highest total score wins; show leaderboard and round breakdown.

APP REQUIREMENTS
1) Platform: Web app (React + TypeScript preferred). Provide a Node/Express backend only if needed; otherwise do client-only with local game state.
2) Modes:
   - Local pass-and-play (same device): hide hands between turns with a “Ready / Hand Reveal” screen.
   - Optional: add online multiplayer via WebSocket as a stretch goal, but local mode is mandatory.
3) UI:
   - Setup screen: player names, number of rounds, optional rules toggles.
   - Round screen: deal animation, hand display, bidding UI, trick area, turn indicator.
   - Enforce legal moves (following suit).
   - Scoreboard screen: running totals + per round detail.
4) Architecture:
   - Separate game engine (pure logic) from UI.
   - Use deterministic shuffling with seed for testing.
   - Provide unit tests for trick resolution, legality of moves, bidding/score calculation, and edge cases (all Escapes, specials interactions).
5) Deliverables:
   - Complete source code with clear folder structure.
   - README with rules implemented, assumptions, how to run, and how to test.
   - A small rules config object so scoring/priority tweaks are easy.
6) Quality:
   - No placeholder logic for core rules.
   - Handle edge cases and invalid actions with user-friendly messages.

IMPLEMENTATION DETAILS
- Create TypeScript types for Card, Suit, Rank, Special, Player, RoundState, TrickState, GameState.
- Implement functions:
   - shuffleDeck(seed)
   - dealHands(players, roundNumber)
   - getLegalMoves(hand, trickState)
   - resolveTrick(trickCards, leadSuit, rulesConfig) -> winner + bonuses info
   - scoreRound(bids, tricksWon, bonuses, roundNumber, rulesConfig)
- Ensure the UI uses the engine functions only.
