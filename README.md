#include <SFML/Graphics.hpp>
#include <vector>
#include <cmath>

const int TILE_SIZE = 50;
const int MAP_WIDTH = 10;
const int MAP_HEIGHT = 10;

struct Unit {
    int x, y;
    int player;
    sf::Color color;
};

int main() {
    sf::RenderWindow window(sf::VideoMode(MAP_WIDTH * TILE_SIZE, MAP_HEIGHT * TILE_SIZE), "Strateji Oyunu");

    sf::RectangleShape tile(sf::Vector2f(TILE_SIZE - 2, TILE_SIZE - 2));
    tile.setFillColor(sf::Color(100, 100, 100));

    std::vector<Unit> units = {
        {0, 0, 1, sf::Color::Blue},
        {1, 0, 1, sf::Color::Blue},
        {2, 0, 1, sf::Color::Blue},

        {7, 9, 2, sf::Color::Red},
        {8, 9, 2, sf::Color::Red},
        {9, 9, 2, sf::Color::Red},
    };

    sf::CircleShape unitShape(TILE_SIZE / 2 - 6);
    unitShape.setOrigin(unitShape.getRadius(), unitShape.getRadius());

    int currentPlayer = 1;
    Unit* selectedUnit = nullptr;

    sf::Font font;
    font.loadFromFile("arial.ttf");  // Bu dosyayı proje klasörüne koy

    while (window.isOpen()) {
        sf::Event event;
        while (window.pollEvent(event)) {
            if (event.type == sf::Event::Closed)
                window.close();

            if (event.type == sf::Event::MouseButtonPressed && event.mouseButton.button == sf::Mouse::Left) {
                int mouseX = event.mouseButton.x;
                int mouseY = event.mouseButton.y;

                int clickedCol = mouseX / TILE_SIZE;
                int clickedRow = mouseY / TILE_SIZE;

                // Henüz seçim yapılmadıysa kendi birimini seç
                if (!selectedUnit) {
                    for (Unit& unit : units) {
                        if (unit.x == clickedCol && unit.y == clickedRow && unit.player == currentPlayer) {
                            selectedUnit = &unit;
                            break;
                        }
                    }
                }
                else {
                    bool isEnemyThere = false;
                    for (auto it = units.begin(); it != units.end(); ++it) {
                        if (it->x == clickedCol && it->y == clickedRow) {
                            if (it->player != currentPlayer &&
                                std::abs(selectedUnit->x - clickedCol) + std::abs(selectedUnit->y - clickedRow) == 1) {
                                units.erase(it);
                                currentPlayer = (currentPlayer == 1) ? 2 : 1;
                                selectedUnit = nullptr;
                                goto endClick;
                            } else {
                                isEnemyThere = true;
                            }
                            break;
                        }
                    }
                        std::abs(selectedUnit->x - clickedCol) + std::abs(selectedUnit->y - clickedRow) == 1) {
                        selectedUnit->x = clickedCol;
                        selectedUnit->y = clickedRow;
                        currentPlayer = (currentPlayer == 1) ? 2 : 1;
                    }
                    selectedUnit = nullptr;
                }
            endClick:;
            }
        }
        window.clear(sf::Color::Black);
        for (int row = 0; row < MAP_HEIGHT; ++row) {
            for (int col = 0; col < MAP_WIDTH; ++col) {
                tile.setPosition(col * TILE_SIZE + 1, row * TILE_SIZE + 1);
                window.draw(tile);
            }
        }
        for (const Unit& unit : units) {
            unitShape.setFillColor(unit.color);
            unitShape.setPosition(unit.x * TILE_SIZE + TILE_SIZE / 2, unit.y * TILE_SIZE + TILE_SIZE / 2);
            window.draw(unitShape);
        }
        if (selectedUnit) {
            sf::RectangleShape highlight(sf::Vector2f(TILE_SIZE - 4, TILE_SIZE - 4));
            highlight.setFillColor(sf::Color(255, 255, 0, 100));
            highlight.setPosition(selectedUnit->x * TILE_SIZE + 2, selectedUnit->y * TILE_SIZE + 2);
            window.draw(highlight);
        }
        window.display();
        int p1 = 0, p2 = 0;
        for (const Unit& u : units) {
            if (u.player == 1) ++p1;
            if (u.player == 2) ++p2;
        }
        if (p1 == 0 || p2 == 0) {
            window.clear();
            sf::Text text;
            text.setFont(font);
            text.setCharacterSize(36);
            text.setFillColor(sf::Color::White);
            text.setString((p1 == 0) ? "Kirmizi Kazandi!" : "Mavi Kazandi!");
            text.setPosition(150, 250);
            window.draw(text);
            window.display();
            sf::sleep(sf::seconds(5));
            window.close();
        }
    }
    return 0;
}
for (const Unit& unit : units) {
    unitShape.setFillColor(unit.color);
    unitShape.setPosition(unit.x * TILE_SIZE + TILE_SIZE / 2, unit.y * TILE_SIZE + TILE_SIZE / 2);
    window.draw(unitShape);

    // Can göstergesi
    sf::Text hpText;
    hpText.setFont(font);
    hpText.setCharacterSize(12);
    hpText.setFillColor(sf::Color::White);
    hpText.setString(std::to_string(unit.hp));
    hpText.setPosition(unit.x * TILE_SIZE + 5, unit.y * TILE_SIZE + 2);
    window.draw(hpText);
}
