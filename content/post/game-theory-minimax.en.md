---
_edit_last: "2"
_last_editor_used_jetpack: block-editor
_thumbnail_id: "2330"
_wpas_done_all: "1"
author: yauheni.shybeka
categories:
  - coding-interview
cover:
  alt: game-theory-feature-image
image: /wp-content/uploads/2021/03/game-theory-feature-image.png
date: "2024-03-27T23:55:34+00:00"
guid: https://itdranik.com/?p=2470
language:
  - English
parent_post_id: null
post_id: "2470"
post_translations:
  - pll_5e4809c57cc17
tags:
  - c#
  - game-theory
title: 'Game Theory: Minimax in C#'
url: /en/game-theory-minimax-en/

---
A cool March evening plunged the city into its usual darkness. The street noise almost disappeared. And only light spring rain was beating rhythmically on the window of the senior developer Vasily. The sounds of rain were comforting, especially after a nervous day at work. Vasily pulled the warm blanket tighter around him. He was determined to go to bed early that day, and everything seemed to help him in this endeavor.

"Knock, knock... knock, knock... Like a drummer! Or even better, like someone tapping on a keyboard in the office. By the way, when will they be opened again?"

Vasily remembered his working desk, the kitchen, the coffee machine, his colleagues: Alex, Anton, Mike. Mike? Oh, no. Not him! Vasily tried to push the thoughts out of his head, but nothing helped. The working day came back to mind: another important online meeting, the manager Alex asks a question and… Vasily jumped out of bed. "Oh, this Alex! Couldn't he wait with his question? I would definitely outplay Mike that time!"

Mike was a senior developer from the frontend department and often crossed paths with Vasily at these important meetings. At one of the meetings a new tradition was born — to play tic-tac-toe. This allowed not only to spend time at the meeting with benefit but also to keep a serious and thoughtful expression on their faces, which definitely corresponded to the general format and was liked by the managers.

Vasily had already had several defeats in a row, so he needed a victory. But an unexpected question broke his concentration, and he made an unfortunate mistake. "Good game!" Mike wrote, which of course meant "how does it feel to lose again?". "Good game!" Vasily replied, meaning "okay, okay, I'll show you next time."

The sleep was finally disturbed. Vasily realized that the next time he needed to be prepared well, and decided to write a bot assistant. The plan was simple and beautiful. Vasily laughed ominously and set to work.

## Minimax on the example of tic-tac-toe 3x3

We will consider the game from the side of the first player (putting crosses). Let a player get **1** point for each win, **-1** for a loss, and **0** for a draw. Consider the following arrangement on the field of play (the first player moves). Below there are the different game developments for possible moves **1** and **2**, where **1** is a cross to-the-left move and **2** is a cross to-the-down move (a cross to-the-right move is similar to the case **1**):

{{< figure src="/wp-content/uploads/2021/03/game-theory-minimax-tic-tac-toe-example.jpg" alt="Different moves in tic-tac-toe 3x3" caption="Different moves in tic-tac-toe 3x3" >}}

If the second player plays **optimally**, the first player will lose (get **-1** point) making moves **1** or **3**, and draw (get **0** points) on move **2**. Therefore, the correct move for the first player will be the move **2**. This is the key idea of the algorithm:

**Minimax** is an algorithm for minimizing losses when a situation develops in the worst-case scenario (in our case, when the opponent plays optimally).

## General case and implementation

The full implementation of the algorithm in C# can be found on the GitHub of the [coding-interview](https://github.com/itdranik/coding-interview) project. Below we will focus on the most interesting points. Let the current state be described by the following set of parameters and methods:

- A set of possible moves for the player, making a move, `GetPossibleMoves`;
- A flag that indicates whether the current state is final (a win for one of the players, or a draw) `IsTerminal`;
- A current active player `ActivePlayer`. There won't be an active player if the state is final;
- And let's also add methods for making and undoing moves `MakeMove / UndoMove`.

```
public interface IState<Player, Move>
    where Player: class
    where Move: class
{
    IList<Move> GetPossibleMoves();
    void MakeMove(Move move);
    void UndoMove();

    bool IsTerminal { get; }
    Player? ActivePlayer { get; }
}
```

As the concepts of `Player` and `Move` can be different for different games, we will describe them using Generic parameters.

Let's go directly to the implementation of the algorithm. As mentioned above, we are trying to minimize losses, that is, to choose a move that, with the optimal game of the second player, will lead to the least losses. In other words, if we can get estimates for all possible states when making a move, then we choose the move that gives us the maximum score.

We introduce the concept of an evaluation function in the context of our algorithm:

**An evaluation function** is a function that, for a selected player, generally gives an approximation for the current state.

```
public interface IScoreFunction<State, Player>
{
    double Calculate(State state, Player player);
}
```

In the example described above, we have already given the basic concept of evaluation for finite states ( **-1**, **0**, **1**). An example implementation of such an evaluation function is presented below:

```
public class MinimaxScoreFunction : IScoreFunction<State, Player>
{
    public double Calculate(State state, Player player)
    {
        if (player == state.Game.FirstPlayer)
        {
            return CalculateForFirstPlayer(state);
        }
        else
        {
            return CalculateForSecondPlayer(state);
        }
    }

    private static double CalculateForFirstPlayer(State state)
    {
        return state.Game.State switch
        {
            GameState.FirstPlayerVictory => 1.0,
            GameState.SecondPlayerVictory => -1.0,
            _ => 0.0
        };
    }

    private static double CalculateForSecondPlayer(MinimaxAdapter state)
    {
        return state.Game.State switch
        {
            GameState.FirstPlayerVictory => -1.0,
            GameState.SecondPlayerVictory => 1.0,
            _ => 0.0
        };
    }
}
```

The question arises: what to do for the intermediate moves? As noted above, the evaluation function gives approximate estimates of the current state in the general case, that is, it is some kind of heuristic. For example, we can assume that two crosses in a row for the first player are better than no crosses and give a score of **0.5** for such a position, but as shown in the example above (moves **1** and **3** satisfy this condition), this approach can lead to defeat.

This problem is solved by the minimax algorithm. Let's consider the tasks the player (Player1) and the opponent (Player2) solve during their turn from the point of view of Player1:

**During the first player turn** (Player1), Player1 must choose a move that will lead him to victory, that is, to **maximize** the result for Player1.

**During the opponent's turn** (Player2), Player2 must choose a move that will lead him to victory. This is equivalent to defeating Player1. That is, from the point of view of Player1, Player2 should choose a move that **minimizes** the result for Player1.

We implement this in the form of a recursive function. This function takes the current state and the player for whom the score is calculated, and returns a pair — the numerical value of the score and the move that must be made to get this score.

```
(double Score, Move? NextMove) Estimate(State state, Player player)
{
    if (state.IsTerminal)
    {
        return
        (
            Score: _scoreFunction.Calculate(state, player),
            NextMove: null
        );
    }

    var possibleMoves = state.GetPossibleMoves();

    var estimations = possibleMoves.Select((move) => {
        state.MakeMove(move);
        var (score, _) = Estimate(state, player, depth.HasValue ? depth - 1 : null);
        state.UndoMove();

        return (Score: score, Move: move);
    });

    var isOpponentTurn = player != state.ActivePlayer;

    return isOpponentTurn
        ? estimations.Aggregate((e1, e2) => e1.Score < e2.Score ? e1 : e2)
        : estimations.Aggregate((e1, e2) => e1.Score > e2.Score ? e1 : e2);
}
```

## Complexity and optimizations

Since there will be a different variety and number of moves for each player depending on the game, the score will be based on some example.

Let's go back to the tic-tac-toe 3x3. Let's assume that the game is played not until one of the players wins, but until the field is completely filled — this will make it easier to calculate the complexity. Then the first player has 9 options for making a move. The second player has only 8 left. Then the first player has 7. And so on. That is, 9! or `O(n!)` variants, where **n** is the number of cells.

If we consider a more complex version of the game with a large field (for example, 20x20), where you need to collect 5 in a row of tic-tac-toe, then obviously there are too many iteration options (400!), so we need to introduce some optimizations.

For example, we can extend our evaluation function to calculate intermediate moves using heuristics and limit the search depth. Or immediately filter out the least profitable moves at the search stage. But as mentioned above, since the heuristic only gives an approximate estimate, the accuracy of the algorithm will become lower.

```
(double Score, Move? NextMove) Estimate(State state, Player player, int? depth)
{
    if (state.IsTerminal)
    {
        return
        (
            Score: _scoreFunction.Calculate(state, player),
            NextMove: null
        );
    }

    if (depth.HasValue && depth <= 0)
    {
        return
        (
            Score: _scoreFunction.Calculate(state, player),
            NextMove: null
        );
    }

    var possibleMoves = state.GetPossibleMoves();
    var estimations = possibleMoves.Select((move) => {
        state.MakeMove(move);
        var (score, _) = Estimate(state, player, depth.HasValue ? depth - 1 : null);
        state.UndoMove();

        return (Score: score, Move: move);
    });

    var isOpponentTurn = player != state.ActivePlayer;

    return isOpponentTurn
        ? estimations.Aggregate((e1, e2) => e1.Score < e2.Score ? e1 : e2)
        : estimations.Aggregate((e1, e2) => e1.Score > e2.Score ? e1 : e2);
}
```

There is also an approach called alpha-beta clipping, which allows you to significantly reduce the number of iterated options without reducing the accuracy. But the consideration of this method deserves a separate article.
