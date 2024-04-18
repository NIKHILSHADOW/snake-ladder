# SNAKES AND LADDERS

## Requirements

### Entities

1. Game
2. Board
3. Players
4. LeaderBoard
5. GameStatus

```mermaid
classDiagram
    class Game {
        - Board board
        - Player[] players
        - Dice[] dices
        - LeaderBoard leaderboard
        - GameStatus gameStatus

        + createGame(GameRequestDTO)
        + int roll()
        + makemove(Player, Piece)
    }

    class Board {
        - int size
        - Cell[] cells
        - Snake[] snakes
        - Ladder[] ladders


    }

    class Snake {
        - int head
        - int tail
    }

    class Ladder {
        - int head
        - int tail
    }

    Board "1" --* "M" Snake
    Board "1" --* "M" Ladder

    Game "1" --* "1" Board


    class Player {
        - String name
        - Piece[] pieces
    }

    Game "1" --* "M" Player

    class Piece {
        - String color
    }

    class Dice {
        - int faces
    }

    Game "1" --* "M" Dice

    class GameStatus {
        <<Enumeration>>
        INPROGRESS
        FINISHED
    }

    Game "1" --* "1" GameStatus

    class Cell {
        - int position
        - Piece[] pieces
    }

    Board "1" --* "M" Cell

    Cell "1" --* "M" Piece

    Player "1" --* "M" Piece



    class LeaderBoard {
        - Player[] players
    }

    Game "1" --* "1" LeaderBoard



```

- In the above design the Snake and Ladder class have duplicate fields
- And also tomorrow there might similar foreign classes like Frog might be added, this shows OCP violation in Board class and SRP violation.

- So we make abstract class

```mermaid
classDiagram
    class Board {
        - int size
        - Cell[] cells
        -Obstacle[] obstacles


    }

    class Obstacle {
        <<abstract>>
        - int head
        - int tail
        + int moveNext()*
    }

    class Snake {
        + int moveNext()
    }

    class Ladder {
        + int moveNext()
    }

    Board "1" --* "M" Obstacle





    Obstacle <|-- Snake
    Obstacle <|--  Ladder
```

- By adding moveNext() function we can extend class, which doesnot depend on tail and head pattern, so it will implement movenext function.

- However If we wanna know if cell contains the Obstacle, we need to search all List of obstacles and find, which is not optimised, so we can make use of Hashmap

```mermaid
    classDiagram
        class Board {
        - int size
        - Cell[] cells
        -Map[Cell, Obstacle] obstacles


    }
```

- Or else we can move obstacles to Cell class.

```mermaid
classDiagram
    class Game {
        - Board board
        - Player[] players
        - Dice[] dices
        - LeaderBoard leaderboard
        - GameStatus gameStatus

        + createGame(GameRequestDTO)
        + int roll()
        + makemove(Player, Piece)
    }

    class Board {
        - int size
        - Cell[] cells


    }

    class Obstacle {
        <<abstract>>
        - int head
        - int tail
        + int moveNext()*
    }

    class Snake {
        + int moveNext()
    }

    class Ladder {
        + int moveNext()
    }

    Obstacle <|-- Snake
    Obstacle <|-- Ladder

    Game "1" --* "1" Board


    class Player {
        - String name
        - Piece[] pieces
    }

    Game "1" --* "M" Player

    class Piece {
        - String color
    }

    class Dice {
        - int faces
    }

    Game "1" --* "M" Dice

    class GameStatus {
        <<Enumeration>>
        INPROGRESS
        FINISHED
    }

    Game "1" --* "1" GameStatus

    class Cell {
        - int position
        - Piece[] pieces
        -Obstacle obstacle
    }

    Cell "1" --* "1" Obstacle
    Board "1" --* "M" Cell

    Cell "1" --* "M" Piece

    Player "1" --* "M" Piece



    class LeaderBoard {
        - Player[] players
    }

    Game "1" --* "1" LeaderBoard

```

- But here Obstacle would be null for basecell, so it adds lot of null checks, so to reduce that we make cell an abstract class

```mermaid
classDiagram
    class Game {
        - Board board
        - Player[] players
        - Dice[] dices
        - LeaderBoard leaderboard
        - GameStatus gameStatus

        + createGame(GameRequestDTO)
        + int roll()
        + makemove(Player, Piece)
    }

    class Board {
        - int size
        - Cell[] cells


    }

    class Snake {
        + int moveNext()
    }

    class Ladder {
        + int moveNext()
    }

    Cell <|-- Snake
    Cell <|-- Ladder
    Cell <|-- BasicCell

    Game "1" --* "1" Board


    class Player {
        - String name
        - Piece[] pieces
    }

    Game "1" --* "M" Player

    class Piece {
        - String color
    }

    class Dice {
        - int faces
    }

    Game "1" --* "M" Dice

    class GameStatus {
        <<Enumeration>>
        INPROGRESS
        FINISHED
    }

    Game "1" --* "1" GameStatus

    class Cell {
        <<abstract>>
        - int position
        - Piece[] pieces
    }

    class BasicCell {
        + int moveNext()
    }

    Board "1" --* "M" Cell

    Cell "1" --* "M" Piece

    Player "1" --* "M" Piece



    class LeaderBoard {
        - Player[] players
    }

    Game "1" --* "1" LeaderBoard

```

- But here in the LeaderBoard, the info is not proper, it is showing the player rank.

```mermaid
classDiagram
    class Game {
        - Board board
        - Player[] players
        - Dice[] dices
        - LeaderBoard leaderboard
        - GameStatus gameStatus

        + createGame(GameRequestDTO)
        + int roll()
        + makemove(Player, Piece)
    }

    class Board {
        - int size
        - Cell[] cells


    }

    class Snake {
        + int moveNext()
    }

    class Ladder {
        + int moveNext()
    }

    Cell <|-- Snake
    Cell <|-- Ladder
    Cell <|-- BasicCell

    Game "1" --* "1" Board


    class Player {
        - String name
        - Piece[] pieces
    }

    Game "1" --* "M" Player

    class Piece {
        - String color
    }

    class Dice {
        - int faces
    }

    Game "1" --* "M" Dice

    class GameStatus {
        <<Enumeration>>
        INPROGRESS
        FINISHED
    }

    Game "1" --* "1" GameStatus

    class Cell {
        <<abstract>>
        - int position
        - Piece[] pieces
    }

    class BasicCell {
        + int moveNext()
    }

    Board "1" --* "M" Cell

    Cell "1" --* "M" Piece

    Player "1" --* "M" Piece



    class LeaderBoard {
        -PlayerRanking[] playerRankings
    }

    class PlayerRanking {
        - Player player
        - int Rank
    }

    LeaderBoard "1" --* "M" PlayerRanking

    Game "1" --* "1" LeaderBoard
```
