# TicTacToeGame

## Опис проекту
Цей проект реалізує гру "Хрестики-нулики" з використанням WPF та різних патернів проєктування. Гра підтримує режими "Грати з AI" та "Грати з другом", зберігає історію ігор та веде рейтинг гравців.

## Функціонал
- **Режими гри**: Гра з AI або з іншим гравцем.
- **Вибір поля**: 3х3,4х4,5х5.
- **Реєстрація та вхід**: Користувачі можуть реєструватися та входити в систему.
- **Історія ігор**: Зберігання та перегляд історії зіграних ігор.
- **Рейтинг**: Ведення рейтингу гравців на основі виграних ігор.
- **Пауза**: Користувач може натиснути на Esc та щоб продовжити Enter.
- **Відмінна ходу**: Є кнопка яка дозволяє відмінти хід 

## Programming Principles
1. **Single Responsibility Principle (SRP)** - Кожен клас має лише одну відповідальність.
    ```csharp
    // PlayerManager.cs
    public class PlayerManager
    {
        // Відповідальний лише за управління гравцями в базі даних
    }
    ```

2. **Open/Closed Principle (OCP)** - Класи відкриті для розширення, але закриті для змін.
    ```csharp
    // IGameStrategy.cs
    public interface IGameStrategy
    {
        void CheckGameState(GameViewModel gameViewModel);
    }

    // PlayerVsAIStrategy.cs
    public class PlayerVsAIStrategy : IGameStrategy
    {
        public void CheckGameState(GameViewModel gameViewModel)
        {
            // Реалізація для AI
        }
    }

    // PlayerVsPlayerStrategy.cs
    public class PlayerVsPlayerStrategy : IGameStrategy
    {
        public void CheckGameState(GameViewModel gameViewModel)
        {
            // Реалізація для гравця проти гравця
        }
    }
    ```

3. **Liskov Substitution Principle (LSP)** - Наслідуючі класи можуть заміняти базові без порушення роботи програми.
    ```csharp
    // Використання IGameStrategy для підстановки різних стратегій гри
    gameViewModel.SetGameStrategy(new PlayerVsAIStrategy());
    ```

4. **Interface Segregation Principle (ISP)** - Клієнти не повинні залежати від інтерфейсів, які вони не використовують.
    ```csharp
    // Інтерфейси IObserver та ISubject
    public interface IObserver
    {
        void Update(string propertyName);
    }

    public interface ISubject
    {
        void Attach(IObserver observer);
        void Detach(IObserver observer);
        void Notify(string propertyName);
    }
    ```

5. **Dependency Inversion Principle (DIP)** - Високорівневі модулі не повинні залежати від низькорівневих модулів. Обидва повинні залежати від абстракцій.
    ```csharp
    // Ін'єкція залежностей для PlayerManager та DatabaseManager
    public GameWindow(bool playWithAI, string username, int boardSize, DatabaseManager dbManager, PlayerManager playerManager)
    {
        InitializeComponent();
        this.dbManager = dbManager;
        this.playerManager = playerManager;
    }
    ```
## Design Patterns
1. **Command Pattern**
    - **Файли**: [MakeMoveCommand.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Commands/MakeMoveCommand.cs), [PauseGameCommand.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Commands/PauseGameCommand.cs), [RegisterPlayerCommand.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Commands/RegisterPlayerCommand.cs)
    - **Пояснення**: Використовується для інкапсуляції запиту як об'єкта, дозволяючи параметризувати клієнтів з різними запитами, чергами або журналами запитів.
    ```csharp
    public class MakeMoveCommand : ICommand
    {
        private readonly GameViewModel _gameViewModel;
        private readonly int _row;
        private readonly int _column;
        private readonly int _player;

        public MakeMoveCommand(GameViewModel gameViewModel, int row, int column, int player)
        {
            _gameViewModel = gameViewModel;
            _row = row;
            _column = column;
            _player = player;
        }

        public void Execute()
        {
            _gameViewModel.MakeMoveInternal(_row, _column, _player);
        }

        public void Undo()
        {
            _gameViewModel.UndoMove(_row, _column, _previousValue);
        }
    }
    ```

2. **Observer Pattern**
    - **Файли**: [IObserver.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Observer/IObserver.cs), [ISubject.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Observer/ISubject.cs), [Subject.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Observer/Subject.cs)
    - **Пояснення**: Використовується для інформування об'єктів-спостерігачів про зміну стану суб'єкта, дозволяючи динамічно додавати/видаляти спостерігачів.
    ```csharp
    public interface IObserver
    {
        void Update(string propertyName);
    }

    public interface ISubject
    {
        void Attach(IObserver observer);
        void Detach(IObserver observer);
        void Notify(string propertyName);
    }

    public class Subject : ISubject
    {
        private List<IObserver> observers = new List<IObserver>();

        public void Attach(IObserver observer)
        {
            observers.Add(observer);
        }

        public void Detach(IObserver observer)
        {
            observers.Remove(observer);
        }

        public void Notify(string propertyName)
        {
            foreach (var observer in observers)
            {
                observer.Update(propertyName);
            }
        }
    }
    ```

3. **Strategy Pattern**
    - **Файли**: [IGameStrategy.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Strategy/IGameStrategy.cs), [PlayerVsAIStrategy.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Strategy/PlayerVsAIStrategy.cs), [PlayerVsPlayerStrategy.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/Strategy/PlayerVsPlayerStrategy.cs)
    - **Пояснення**: Використовується для визначення сімейства алгоритмів, інкапсуляції кожного з них і забезпечення їх взаємозамінності.
    ```csharp
    public interface IGameStrategy
    {
        void CheckGameState(GameViewModel gameViewModel);
    }

    public class PlayerVsAIStrategy : IGameStrategy
    {
        public void CheckGameState(GameViewModel gameViewModel)
        {
            if (gameViewModel.CheckForWinner())
            {
                gameViewModel.OnPropertyChanged("Winner");
                gameViewModel.Notify("Winner");
            }
            else if (gameViewModel.IsDraw())
            {
                gameViewModel.OnPropertyChanged("Draw");
                gameViewModel.Notify("Draw");
            }
            else
            {
                gameViewModel.ChangePlayer();
                if (gameViewModel.CurrentPlayer == 2)
                {
                    gameViewModel.PerformAiMove();
                }
            }
        }
    }

    public class PlayerVsPlayerStrategy : IGameStrategy
    {
        public void CheckGameState(GameViewModel gameViewModel)
        {
            if (gameViewModel.CheckForWinner())
            {
                gameViewModel.OnPropertyChanged("Winner");
                gameViewModel.Notify("Winner");
            }
            else if (gameViewModel.IsDraw())
            {
                gameViewModel.OnPropertyChanged("Draw");
                gameViewModel.Notify("Draw");
            }
            else
            {
                gameViewModel.ChangePlayer();
            }
        }
    }
    ```
    ## Refactoring Techniques
1. **Extract Class**
    - **Файли**: [PlayerManager.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeDataAccess/PlayerManager.cs), [DatabaseManager.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeDataAccess/DatabaseManager.cs)
    - **Пояснення**: Виділення логіки роботи з базою даних в окремий клас для полегшення коду і підвищення його підтримуваності.
    ```csharp
    public class PlayerManager
    {
        private readonly string _databasePath;

        public PlayerManager(string databasePath)
        {
            _databasePath = databasePath;
        }

        public void InsertPlayer(Player player)
        {
            using (var connection = new SQLiteConnection($"Data Source={_databasePath};Version=3;"))
            {
                connection.Open();
                var command = new SQLiteCommand(
                    "INSERT INTO Players (Username, Email, PasswordHash) VALUES (@Username, @Email, @PasswordHash)", connection);
                command.Parameters.AddWithValue("@Username", player.Username);
                command.Parameters.AddWithValue("@Email", player.Email);
                command.Parameters.AddWithValue("@PasswordHash", player.PasswordHash);
                command.ExecuteNonQuery();
            }
        }

        // Інші методи управління гравцями...
    }
    ```

2. **Extract Method**
    - **Файли**: [GameWindow.xaml.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeGame/GameWindow.xaml.cs)
    - **Пояснення**: Виділення логіки в окремі методи для підвищення читабельності і повторного використання коду.
    ```csharp
    private void InitializeGame()
    {
        MainGrid.Children.Clear();
        MainGrid.RowDefinitions.Clear();
        MainGrid.ColumnDefinitions.Clear();

        for (int i = 0; i < gameViewModel.BoardSize; i++)
        {
            MainGrid.RowDefinitions.Add(new RowDefinition());
            MainGrid.ColumnDefinitions.Add(new ColumnDefinition());
        }

        for (int i = 0; i < gameViewModel.BoardSize; i++)
        {
            for (int j = 0; j < gameViewModel.BoardSize; j++)
            {
                Button button = new Button
                {
                    FontSize = 32,
                    Content = string.Empty
                };
                button.Click += Button_Click;
                Grid.SetRow(button, i);
                Grid.SetColumn(button, j);
                MainGrid.Children.Add(button);
            }
        }
    }
    ```

3. **Introduce Parameter Object**
    - **Файли**: [Player.cs](https://github.com/Alexdabd22/Software-Design/blob/main/Lab6/TicTacToeGame/TicTacToeModels/Models/PlayerModel.cs)
    - **Пояснення**: Використання класу Player для передачі параметрів замість використання декількох параметрів у методах.
    ```csharp
    public class Player
    {
        public int PlayerID { get; set; }
        public string Username { get; set; }
        public string Email { get; set; }
        public string PasswordHash { get; set; }
        public DateTime CreatedAt { get; set; }

        public Player(int playerID, string username, string email, string passwordHash, DateTime createdAt)
        {
            PlayerID = playerID;
            Username = username;
            Email = email;
            PasswordHash = passwordHash;
            CreatedAt = createdAt;
        }
    }
    ```

## Використання Git
- Кожна фіча розробляється в окремій гілці.
- Використовуються пулл-ріквести для мерджу гілок.
