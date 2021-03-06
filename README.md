# Kata Minesweeper

## The problem

### Minesweeper game

Have you ever played Minesweeper? It’s a cute little game which comes within a certain Operating System whose name we can’t really remember. 
Well, the goal of the game is to find all the mines within an MxN field. 

<p align="center">
  <img src="minesweeper.jpg">
</p>

To help you, the game shows a number in a square which tells you how many mines there are adjacent to that square. 
For instance, take the following 4x4 field with 2 mines (which are represented by an * character):
```bash
*...
....
.*..
....
```
The same field including the hint numbers described above would look like this:
```bash
*100
2210
1*10
1110
```

More info: [microsoft minesweeper](https://en.wikipedia.org/wiki/Microsoft_Minesweeper)

### Problem Description

#### Input

You should write a program that takes input as follows:

- The input will consist of an arbitrary number of fields. 

- The first line of each field contains two integers n and m (0 < n,m <= 100) which stands for the number of lines and 
columns of the field respectively. 

- The next n lines contains exactly m characters and represent the field. 

- Each safe square is represented by an “.” character (without the quotes) and each mine square is represented 
by an “*” character (also without the quotes). 


#### Output

Your program should produce output as follows:

For each field, you must print the following message in a line alone:

Field #x:

Where x stands for the number of the field (starting from 1). 

The next n lines should contain the field with the “.” characters replaced by the number of adjacent mines to that square. 
There must be an empty line between field outputs.

### Clues

As you may have already noticed, each square may have at most 8 adjacent squares.

### Suggested Test Cases

This is the acceptance test input:
```bash
4 4
*...
....
.*..
....
3 5
**...
.....
.*...
```
and output:
```bash
Field #1:
*100
2210
1*10
1110

Field #2:
**100
33200
1*100
```

## The solution

The solution has been coded doing TDD, check git logs.

### Environment
```bash
elixir -v
Erlang/OTP 20 [erts-9.0.4] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]

Elixir 1.5.1

```
### Running the tests
```bash
mix test
```

### Build and run the app
```bash
mix escript.build
./kata_minesweeper --path=test/resources/simple_grid.txt
```

### Approach

There is one single entry point for the application, it has the functionality of facade and it orchestrate the different parts of the implementation:

1. Parse the input & create the grids
2. Generate grids with the hints (the algorithm)
3. Generate the expected output


Entry point: (mines_weeper.ex)

```elixir
Minesweeper.sweep "path_to_your_file"
```

##### 1. Parsing the input from file (grid_parser.ex):

From:
```bash
4 4
*...
....
.*..
....
```

To:

```bash
  0 1 2 3
---------
0|* . . .
1|. . . . 
2|. * . .
3|. . . .
```
The grid representation is an elixir struct:

```elixir
@type hint :: 0..8
@type square_value :: (:mine | :safe | hint)
@type position :: {non_neg_integer, non_neg_integer}
@type squares :: %{position => square_value}
@type t :: %Minesweeper.Grid{size: {non_neg_integer, non_neg_integer}, squares: squares}
```

Where:
- '*' is a mine (:mine)
- '.' is a safe square (:safe)


##### 2. Generate grid with the hints, the main algorithm  (grid.ex)

```bash
  0 1 2 3 x
---------
0|* 1 0 0 
1|2 2 1 0  
2|1 * 1 0 
3|1 1 1 0 
y
```

The algorithm is simple, recursive and tail call optimized:

- Given a grid and a current position in the grid
- Sum all mines of its adjacent squares
- Generate a new grid with the hint in that position
- Call recursively same method with the next position in ghe grid to evaluate


##### 3. Generate the output (console_writer.ex):

The output implements a behaviour, so can be replaced easily by another kind of output.
```bash
Field #1:
*100
2210
1*10
1110
```

### Out of scope / to improve

- Input file validation
- Error handling
- File input parser could be improved: at least make it recursive and tail call optimized
- File output parser could be improved, at least make it recursive and tail call optimized
- Add documentation
- Add Logging