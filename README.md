# csh-backtrack-recursion-queens

Алгоритм решает задачу о **8 ферзях**, используя метод **обратного отслеживания** (backtracking) для размещения 8 ферзей на шахматной доске 8x8 таким образом, чтобы ни один ферзь не атаковал другого.
Рекурсивно пытаемся разместить ферзей на доске. Если размещение безопасно (ни один ферзь не атакует другого), продолжаем рекурсию с оставшимися позициями. Если не удаётся найти решение, убираем ферзя и пробуем другую позицию.

### 1. Псевдокод :

```
function queens(board)
    if board has 8 queens then
        return board  // Решение найдено

    for each position in board.unattacked_positions do
        board.place_queen(position)  // Пытаемся поставить ферзя

        solution = queens(board)  // Рекурсивно пытаемся найти решение
        if solution then
            return solution  // Если решение найдено, возвращаем его

        board.remove_queen(position)  // Убираем ферзя, если не удалось найти решение
    
    return False  // Возвращаем False, если решение не найдено
end function
```

### 2. C# реализация Вариант 1:
```csharp
using System;
using System.Linq;

class GFG {

static readonly int N = 8;

// function to check if it is safe to place
// a queen at a particular position
static bool isSafe(int[,] board, int row, int col)
{

	// check if there is a queen in the same row to the
	// left
	for (int x = 0; x < col; x++)
	if (board[row, x] == 1)
		return false;

	// check if there is a queen in the upper left
	// diagonal
	for (int x = row, y = col; x >= 0 && y >= 0;
		x--, y--)
	if (board[x, y] == 1)
		return false;

	// check if there is a queen in the lower left
	// diagonal
	for (int x = row, y = col; x < N && y >= 0;
		x++, y--)
	if (board[x, y] == 1)
		return false;

	// if there is no queen in any of the above
	// positions, then it is safe to place a queen
	return true;
}

// function to solve the N-queens problem using
// backtracking
static bool solveNQueens(int[,] board, int col)
{

	// if all queens are placed, print the board
	if (col == N) {
	for (int i = 0; i < N; i++) {
		for (int j = 0; j < N; j++) {
		Console.Write(board[i, j] + " ");
		}
		Console.WriteLine();
	}
	Console.WriteLine();
	return true;
	}

	// try placing a queen in each row of the current
	// column
	for (int i = 0; i < N; i++) {
	if (isSafe(board, i, col)) {
		board[i, col] = 1; // place the queen
		if (solveNQueens(board, col + 1))
		return true;

		// backtrack if placing the queen doesn't
		// lead to a solution
		board[i, col] = 0;
	}
	}

	// if no solution is found, return false
	return false;
}

// initialize the board with zeros and call the
// solveNQueens function to solve the problem
public static void Main(string[] args)
{
	int[,] board = new int[N, N];
	if (!solveNQueens(board, 0))
	Console.WriteLine("No solution found");
}
}

```

### 3. C# реализация Вариант 2:

```csharp
using System;
using System.Collections.Generic;

public class NQueensSolver
{
    public class Board
    {
        public int Size { get; private set; }
        private bool[,] board;

        public Board(int size)
        {
            Size = size;
            board = new bool[size, size];
        }

        // Метод для проверки, есть ли 8 ферзей на доске
        public bool Has8Queens()
        {
            int queenCount = 0;
            for (int row = 0; row < Size; row++)
            {
                for (int col = 0; col < Size; col++)
                {
                    if (board[row, col]) queenCount++;
                }
            }
            return queenCount == 8;
        }

        // Метод для нахождения безопасных позиций, где можно поставить ферзя
        public List<(int, int)> GetUnattackedPositions()
        {
            List<(int, int)> positions = new List<(int, int)>();
            for (int row = 0; row < Size; row++)
            {
                for (int col = 0; col < Size; col++)
                {
                    if (IsSafe(row, col))
                    {
                        positions.Add((row, col));
                    }
                }
            }
            return positions;
        }

        // Проверяем, можно ли безопасно поставить ферзя в позицию (row, col)
        private bool IsSafe(int row, int col)
        {
            // Проверка строки, столбца и диагоналей
            for (int i = 0; i < Size; i++)
            {
                if (board[row, i] || board[i, col]) return false;
            }

            // Проверка диагоналей
            for (int i = 0; i < Size; i++)
            {
                for (int j = 0; j < Size; j++)
                {
                    if (Math.Abs(row - i) == Math.Abs(col - j) && board[i, j]) return false;
                }
            }

            return true;
        }

        // Метод для размещения ферзя на доске
        public void PlaceQueen(int row, int col)
        {
            board[row, col] = true;
        }

        // Метод для удаления ферзя с доски
        public void RemoveQueen(int row, int col)
        {
            board[row, col] = false;
        }

        // Вывод доски
        public void PrintBoard()
        {
            for (int row = 0; row < Size; row++)
            {
                for (int col = 0; col < Size; col++)
                {
                    Console.Write(board[row, col] ? "Q " : ". ");
                }
                Console.WriteLine();
            }
            Console.WriteLine();
        }
    }

    // Решение задачи о 8 ферзях
    public static Board Queens(Board board)
    {
        if (board.Has8Queens())
        {
            return board;  // Решение найдено
        }

        foreach (var (row, col) in board.GetUnattackedPositions())
        {
            board.PlaceQueen(row, col);  // Пытаемся разместить ферзя
            Board solution = Queens(board);  // Рекурсивно ищем решение

            if (solution != null)  // Если решение найдено, возвращаем доску
            {
                return solution;
            }

            board.RemoveQueen(row, col);  // Удаляем ферзя, если это не привело к решению
        }

        return null;  // Решение не найдено
    }

    public static void Main(string[] args)
    {
        Board board = new Board(8);  // Инициализируем доску 8x8
        Board solution = Queens(board);  // Ищем решение

        if (solution != null)
        {
            Console.WriteLine("Solution found:");
            solution.PrintBoard();
        }
        else
        {
            Console.WriteLine("No solution found.");
        }
    }
}
```
