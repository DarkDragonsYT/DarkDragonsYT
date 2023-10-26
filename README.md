// Змінні для розміру поля, швидкості гри та керування
const int width = 20;
const int height = 20;
const int fps = 10;
const char up = 'w';
const char down = 's';
const char left = 'a';
const char right = 'd';

// Структура для збереження координат точки
struct Point {
    int x;
    int y;
};

// Клас для реалізації логіки гри
class SnakeGame {
    // Змінні для збереження стану гри
    Point snake[100]; // масив точок, що утворюють змійку
    int snakeLength; // довжина змійки
    Point food; // координати їжі
    bool gameOver; // чи закінчилася гра
    char direction; // напрямок руху змійки

public:
    // Конструктор класу
    SnakeGame() {
        // Ініціалізуємо змінні початковими значеннями
        snakeLength = 1; // початкова довжина змійки - 1
        snake[0].x = width / 2; // початкова позиція змійки - центр поля
        snake[0].y = height / 2;
        food.x = rand() % width; // випадкова позиція їжі
        food.y = rand() % height;
        gameOver = false; // гра ще не закінчилася
        direction = ' '; // напрямок руху - невизначений
    }

    // Метод для малювання поля та елементів гри
    void draw() {
        system("cls"); // очищуємо екран

        // Малюємо верхню межу поля
        for (int i = 0; i < width + 2; i++) {
            cout << "#";
        }
        cout << "\n";

        // Малюємо бокові межі поля та елементи гри
        for (int i = 0; i < height; i++) {
            for (int j = 0; j < width + 2; j++) {
                if (j == 0 || j == width + 1) {
                    cout << "#"; // бокова межа поля
                }
                else if (i == snake[0].y && j == snake[0].x) {
                    cout << "O"; // голова змійки
                }
                else if (i == food.y && j == food.x) {
                    cout << "*"; // їжа
                }
                else {
                    bool isBody = false; // чи є поточна точка частиною тіла змійки
                    for (int k = 1; k < snakeLength; k++) {
                        if (i == snake[k].y && j == snake[k].x) {
                            cout << "o"; // тіло змійки
                            isBody = true;
                            break;
                        }
                    }
                    if (!isBody) {
                        cout << " "; // порожня клітинка поля
                    }
                }
            }
            cout << "\n";
        }

        // Малюємо нижню межу поля
        for (int i = 0; i < width + 2; i++) {
            cout << "#";
        }
        cout << "\n";

        // Виводимо інструкції для користувача
        cout << "Use " << up << ", " << down << ", " << left << ", " << right << " to move the snake.\n";
        cout << "Press x to exit the game.\n";
    }

    // Метод для обробки вводу користувача
    void input() {
        // Якщо користувач натиснув якусь клавішу
        if (_kbhit()) {
            // Зчитуємо код клавіші
            char key = _getch();
            // Змінюємо напрямок руху змійки відповідно до клавіші
            switch (key) {
            case up:
                direction = up;
                break;
            case down:
                direction = down;
                break;
            case left:
                direction = left;
                break;
            case right:
                direction = right;
                break;
            case 'x':
                gameOver = true; // завершуємо гру
                break;
            }
        }
    }

    // Метод для оновлення логіки гри
    void update() {
        // Зсуваємо координати тіла змійки на один крок назад
        for (int i = snakeLength - 1; i > 0; i--) {
            snake[i].x = snake[i - 1].x;
            snake[i].y = snake[i - 1].y;
        }

        // Змінюємо координати голови змійки відповідно до напрямку руху
        switch (direction) {
        case up:
            snake[0].y--;
            break;
        case down:
            snake[0].y++;
            break;
        case left:
            snake[0].x--;
            break;
        case right:
            snake[0].x++;
            break;
        }

        // Перевіряємо, чи змійка не вийшла за межі поля
        if (snake[0].x < 1 || snake[0].x > width || snake[0].y < 0 || snake[0].y > height) {
            gameOver = true; // якщо так, то завершуємо гру
        }

        // Перевіряємо, чи змійка не з'їла себе
        for (int i = 1; i < snakeLength; i++) {
            if (snake[0].x == snake[i].x && snake[0].y == snake[i].y) {
                gameOver = true; // якщо так, то завершуємо гру
            }
        }

        // Перевіряємо, чи змійка не з'їла їжу
        if (snake[0].x == food.x && snake[0].y == food.y) {
            snakeLength++; // якщо так, то збільшуємо довжину змійки
            food.x = rand() % width; // генеруємо нову позицію їжі
            food.y = rand() % height;
        }
    }

    // Метод для перевірки, чи закінчилася гра
    bool isGameOver() {
        return gameOver;
    }
};

// Головна функція програми
int main() {
    srand(time(NULL)); // ініціалізуємо генератор випадкових чисел

    SnakeGame game; // створюємо об'єкт гри

    // Головний цикл гри
    while (!game.isGameOver()) {
        game.draw(); // малюємо поле та елементи гри
        game.input(); // обробляємо ввід користувача
        game.update(); // оновлюємо логіку гри
        Sleep(1000 / fps); // робимо паузу для контролю швидкості гри
    }

    cout << "Game over!\n"; // виводимо повідомлення про кінець гри

    return 0; // завершуємо програму
}
