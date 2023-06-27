using System;

namespace Connect4
{
    class Program
    {
        static void Main(string[] args)
        {
            GameController gameController = new GameController();
            gameController.StartGame();
        }
    }

    class GameController
    {
        private GameModel gameModel;
        private Player currentPlayer;
        private Player player1;
        private Player player2;

        public GameController()
        {
            gameModel = new GameModel();
            player1 = new HumanPlayer('X');
            player2 = new ComputerPlayer('O');
        }

        public void StartGame()
        {
            currentPlayer = player1;

            while (!gameModel.GameOver)
            {
                DisplayBoard();
                GetPlayerMove();

                if (gameModel.CheckForWin(currentPlayer.Symbol))
                {
                    DisplayBoard();
                    Console.WriteLine("Player " + currentPlayer.Symbol + " wins!");
                    gameModel.GameOver = true;
                }
                else if (gameModel.CheckForDraw())
                {
                    DisplayBoard();
                    Console.WriteLine("It's a draw!");
                    gameModel.GameOver = true;
                }
                else
                {
                    SwitchPlayer();
                }
            }

            Console.WriteLine("Game Over!");
            Console.ReadLine();
        }

        private void DisplayBoard()
        {
            Console.Clear();
            gameModel.DisplayBoard();
            Console.WriteLine();
        }

        private void GetPlayerMove()
        {
            int column;
            bool validMove = false;

            while (!validMove)
            {
                Console.Write("Player " + currentPlayer.Symbol + ", enter a column number: ");
                string input = Console.ReadLine();

                if (int.TryParse(input, out column) && column >= 1 && column <= 7)
                {
                    column--;

                    if (gameModel.IsColumnFull(column))
                    {
                        Console.WriteLine("Column is full. Please choose another column.");
                    }
                    else
                    {
                        gameModel.PlaceDisc(column, currentPlayer.Symbol);
                        validMove = true;
                    }
                }
                else
                {
                    Console.WriteLine("Invalid input. Please enter a column number (1-7).");
                }
            }
        }

        private void SwitchPlayer()
        {
            currentPlayer = (currentPlayer == player1) ? player2 : player1;
        }
    }

    class GameModel
    {
        private char[,] board;
        public bool GameOver { get; set; }

        public GameModel()
        {
            board = new char[6, 7];
            GameOver = false;
        }

        public void DisplayBoard()
        {
            for (int row = 0; row < 6; row++)
            {
                for (int col = 0; col < 7; col++)
                {
                    Console.Write(board[row, col] + " ");
                }
                Console.WriteLine();
            }

            Console.WriteLine("1 2 3 4 5 6 7");
        }

        public bool IsColumnFull(int column)
        {
            return board[0, column] != '\0';
        }

        public void PlaceDisc(int column, char symbol)
        {
            for (int row = 5; row >= 0; row--)
            {
                if (board[row, column] == '\0')
                {
                    board[row, column] = symbol;
                    break;
                }
            }
        }

        public bool CheckForWin(char symbol)
        {
            // Check for horizontal win
            for (int row = 0; row < 6; row++)
            {
                for (int col = 0; col < 4; col++)
                {
                    if (board[row, col] == symbol &&
                        board[row, col + 1] == symbol &&
                        board[row, col + 2] == symbol &&
                        board[row, col + 3] == symbol)
                    {
                        return true;
                    }
                }
            }

            // Check for vertical win
            for (int row = 0; row < 3; row++)
            {
                for (int col = 0; col < 7; col++)
                {
                    if (board[row, col] == symbol &&
                        board[row + 1, col] == symbol &&
                        board[row + 2, col] == symbol &&
                        board[row + 3, col] == symbol)
                    {
                        return true;
                    }
                }
            }

            // Check for diagonal win (top-left to bottom-right)
            for (int row = 0; row < 3; row++)
            {
                for (int col = 0; col < 4; col++)
                {
                    if (board[row, col] == symbol &&
                        board[row + 1, col + 1] == symbol &&
                        board[row + 2, col + 2] == symbol &&
                        board[row + 3, col + 3] == symbol)
                    {
                        return true;
                    }
                }
            }

            // Check for diagonal win (top-right to bottom-left)
            for (int row = 0; row < 3; row++)
            {
                for (int col = 3; col < 7; col++)
                {
                    if (board[row, col] == symbol &&
                        board[row + 1, col - 1] == symbol &&
                        board[row + 2, col - 2] == symbol &&
                        board[row + 3, col - 3] == symbol)
                    {
                        return true;
                    }
                }
            }

            return false;
        }

        public bool CheckForDraw()
        {
            for (int col = 0; col < 7; col++)
            {
                if (!IsColumnFull(col))
                {
                    return false;
                }
            }

            return true;
        }
    }

    abstract class Player
    {
        public char Symbol { get; protected set; }
    }

    class HumanPlayer : Player
    {
        public HumanPlayer(char symbol)
        {
            Symbol = symbol;
        }
    }

    class ComputerPlayer : Player
    {
        public ComputerPlayer(char symbol)
        {
            Symbol = symbol;
        }
    }
}
