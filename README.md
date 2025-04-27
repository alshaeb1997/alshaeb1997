include <iostream>
#include <string>
#include <vector>
#include <cstdlib>
#include <ctime>
using namespace std;

// Structure to represent a country
struct Country {
    string name;
    int soldiers; // Number of soldiers
    int strength; // Total strength (soldiers * individual strength)
    bool isConquered; // Has the country been conquered?
};

// Structure to represent the player
struct Player {
    string name;
    int soldiers;
    int strengthPerSoldier;
    int totalStrength;
    vector<string> conqueredCountries; // List of conquered countries
};

// Function to update player's strength
void updatePlayerStrength(Player& player) {
    player.totalStrength = player.soldiers * player.strengthPerSoldier;
}

// Function to display player's status
void displayPlayerStatus(const Player& player) {
    cout << "\nPlayer Status: " << player.name << endl;
    cout << "Number of Soldiers: " << player.soldiers << endl;
    cout << "Total Strength: " << player.totalStrength << endl;
    cout << "Conquered Countries: ";
    if (player.conqueredCountries.empty()) {
        cout << "None yet.";
    } else {
        for (const auto& country : player.conqueredCountries) {
            cout << country << " ";
        }
    }
    cout << endl;
}

// Function to display the status of countries
void displayCountries(const vector<Country>& countries) {
    cout << "\nWorld Countries Status:" << endl;
    for (const auto& country : countries) {
        if (!country.isConquered) {
            cout << country.name << " - Soldiers: " << country.soldiers 
                 << ", Strength: " << country.strength << endl;
        }
    }
}

// Function to train soldiers
void trainSoldiers(Player& player) {
    int choice;
    cout << "\nTrain Soldiers:" << endl;
    cout << "1. Increase soldier count (1000 gold for 100 soldiers)" << endl;
    cout << "2. Increase soldier strength (5000 gold for +1 strength)" << endl;
    cout << "Choose (1 or 2): ";
    cin >> choice;

    if (choice == 1) {
        player.soldiers += 100;
        cout << "Added 100 soldiers!" << endl;
    } else if (choice == 2) {
        player.strengthPerSoldier += 1;
        cout << "Increased soldier strength by 1!" << endl;
    }
    updatePlayerStrength(player);
}

// Function to attack a country
bool attackCountry(Player& player, Country& country, int stage) {
    cout << "\nAttacking " << country.name << "!" << endl;
    cout << "Your Strength: " << player.totalStrength << " | Enemy Strength: " << country.strength << endl;

    if (player.totalStrength > country.strength) {
        cout << "Victory! You have conquered " << country.name << "!" << endl;
        country.isConquered = true;
        player.conqueredCountries.push_back(country.name);
        player.soldiers -= country.soldiers / 10; // Lose some soldiers
        updatePlayerStrength(player);
        return true;
    } else {
        cout << "Defeat! Your strength is insufficient." << endl;
        player.soldiers -= country.soldiers / 5; // Lose more soldiers
        updatePlayerStrength(player);
        return false;
    }
}

// Function to increase difficulty for each stage
void increaseDifficulty(vector<Country>& countries, int stage) {
    for (auto& country : countries) {
        if (!country.isConquered) {
            country.soldiers += country.soldiers * (0.1 * stage); // Increase soldier count
            country.strength = country.soldiers * (5 + stage); // Increase strength
        }
    }
}

// Main function
int main() {
    srand(time(0)); // Seed for random numbers
    cout << "Welcome to Conquer the World!" << endl;

    // Setup player
    Player player;
    cout << "Enter your name: ";
    cin >> player.name;
    player.soldiers = 500; // Initial soldier count
    player.strengthPerSoldier = 5; // Initial strength per soldier
    updatePlayerStrength(player);

    // Setup countries (expanded world map)
    vector<Country> countries = {
        {"Egypt", 1000, 1000 * 5, false},
        {"Saudi Arabia", 1200, 1200 * 5, false},
        {"Syria", 800, 800 * 5, false},
        {"Lebanon", 600, 600 * 5, false},
        {"Palestine", 500, 500 * 5, false},
        {"Jordan", 700, 700 * 5, false},
        {"Iraq", 900, 900 * 5, false},
        {"Turkey", 1500, 1500 * 5, false},
        {"United States", 2000, 2000 * 5, false},
        {"Russia", 1800, 1800 * 5, false},
        {"China", 2200, 2200 * 5, false},
        {"France", 1100, 1100 * 5, false},
        {"Germany", 1200, 1200 * 5, false},
        {"United Kingdom", 1000, 1000 * 5, false},
        {"Italy", 900, 900 * 5, false},
        {"Spain", 850, 850 * 5, false},
        {"Poland", 800, 800 * 5, false},
        {"Netherlands", 700, 700 * 5, false}
    };

    int stage = 1;
    bool gameOver = false;

    // Game loop
    while (!gameOver) {
        cout << "\n=== Stage " << stage << " ===" << endl;
        displayPlayerStatus(player);
        displayCountries(countries);

        int choice;
        cout << "\nWhat would you like to do?" << endl;
        cout << "1. Train soldiers" << endl;
        cout << "2. Attack a country" << endl;
        cout << "3. Exit" << endl;
        cout << "Choose (1-3): ";
        cin >> choice;

        if (choice == 1) {
            trainSoldiers(player);
        } else if (choice == 2) {
            cout << "\nChoose a country to attack (enter the number):" << endl;
            int countryIndex = 0;
            int displayIndex = 1;
            vector<int> validIndices;
            for (int i = 0; i < countries.size(); ++i) {
                if (!countries[i].isConquered) {
                    cout << displayIndex << ". " << countries[i].name << endl;
                    validIndices.push_back(i);
                    displayIndex++;
                }
            }
            cin >> countryIndex;
            countryIndex--; // Adjust for 0-based indexing
            if (countryIndex >= 0 && countryIndex < validIndices.size()) {
                attackCountry(player, countries[validIndices[countryIndex]], stage);
            } else {
                cout << "Invalid choice!" << endl;
            }
        } else if (choice == 3) {
            cout << "Thanks for playing!" << endl;
            break;
        }

        // Check for stage completion
        bool allConquered = true;
        for (const auto& country : countries) {
            if (!country.isConquered) {
                allConquered = false;
                break;
            }
        }
        if (allConquered) {
            cout << "\nCongratulations! You have conquered all countries in Stage " << stage << "!" << endl;
            stage++;
            // Reset countries with increased difficulty
            for (auto& country : countries) {
                country.isConquered = false;
                country.soldiers += 500 * stage;
                country.strength = country.soldiers * (5 + stage);
            }
            increaseDifficulty(countries, stage);
        }

        // Check for game over (loss)
        if (player.soldiers <= 0) {
            cout << "\nGame Over! You have no soldiers left." << endl;
            gameOver = true;
        }
    }

    return 0;
}
