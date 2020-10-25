#include <iostream>
#include <string>
#include <vector>
#include <ctime>
#include <conio.h>
#include<Windows.h>

enum Type { Hearts, Dimonds, Clovers, Spare, Size };
enum Number {
    to, tre, fire, fem, seks, sju, otte, ni, ti, J, Q, K, A, size
};
struct Cardtype
{
    Type draw;
    Number number;
};
struct heimer {
    int value;
    Cardtype cardFace;
    std::string display;
};

class Hand {
    int handTotal = 0;
public:
    std::vector<heimer> cards;
    void AddCard(heimer _cards)
    {
        //add card
        cards.push_back(_cards);
        handTotal += _cards.value;
        //checkin the card if it matches ace if it does and the total value
        if (_cards.cardFace.number == A && handTotal > 21) {
            handTotal -= 10;
        }
    }
    int CalculaterHandSum() {
        return handTotal;
    }
};

std::string TypeString[]{ "H", "D", "C", "S", };
std::string NumberString[]{ "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A" };

std::vector<heimer> Library;
std::vector<heimer> graveyard;
heimer GetRandomCard();

heimer GetRandomCard()
{
    int randomint = rand() % Library.size();
    graveyard.push_back(Library.at(randomint));
    Library.erase(Library.begin() + randomint);
    return graveyard.at(graveyard.size() - 1);

}
void ReShuffleDeck();
void ReShuffleDeck() {
    for (int i = 0; i < graveyard.size(); i++) {
        Library.push_back(graveyard.at(i));
    }
    graveyard.erase(graveyard.begin(), graveyard.end());

}
void DrawUI(Hand& Player, Hand& Dealer)
{
    system("cls");
    std::cout << "Dealers hand:" << std::endl;
    for (int i = 0; i < Dealer.cards.size(); i++) {
        std::string n = Dealer.cards.at(i).display;
        std::cout << "|----" << n << "----|";
    }
    std::cout << std::endl;
    std::cout << "Sum: " << Dealer.CalculaterHandSum();
    std::cout << std::endl;
    std::cout << std::endl;
    std::cout << "Your hand:" << std::endl;
    for (int i = 0; i < Player.cards.size(); i++)
    {
        std::string n = Player.cards.at(i).display;
        std::cout << "|----" << n << "----|";
    }
    std::cout << std::endl;
    std::cout << "Sum: " << Player.CalculaterHandSum();
    std::cout << std::endl;
    printf("1. hit \n 2.stand\n");
}
int main()
{
    Hand Dealer;
    Hand Player;
    srand(time(NULL));
    heimer _Card;

    for (int i = 0; i < Type::Size; i++) {
        for (int j = 0; j < Number::size; j++) {
            _Card.cardFace.draw = (Type)i;
            _Card.cardFace.number = (Number)j;
            _Card.display = NumberString[j] + " of " + TypeString[i];

            //setting j q k a to 10 points
            switch (_Card.cardFace.number)
            {
            case J:
            {
                _Card.value = 10;
            }
            break;
            case Q:
            {
                _Card.value = 10;
            }
            break;
            case K:
            {
                _Card.value = 10;
            }
            break;
            case A:
            {
                _Card.value = 11;
            }
            break;
            default:
            {
                _Card.value = j + 2;
            }
            break;
            }
            Library.push_back(_Card);
        }
    }

    //game loop
    do
    {
        bool bRound = true;
        Hand Dealer;
        Hand Player;

        Player.AddCard(GetRandomCard());
        Player.AddCard(GetRandomCard());
        Dealer.AddCard(GetRandomCard());
        Dealer.AddCard(GetRandomCard());
        DrawUI(Player, Dealer);

        if (Player.CalculaterHandSum() == 21)
        {
            std::cout << "Player blackjack!" << std::endl;
            Sleep(2000);
            bRound = false;
            break;
        }
        do {
            char input = _getch();
            if (input == '1')
            {
                Player.AddCard(GetRandomCard());
                DrawUI(Player, Dealer);
                const auto handSum = Player.CalculaterHandSum();
                if (handSum > 21)
                {
                    std::cout << "Player busted!" << std::endl;
                    Sleep(2000);
                    bRound = false;
                    break;
                }
                if (handSum == 21)
                {
                    std::cout << "Player blackjack!" << std::endl;
                    Sleep(2000);
                    bRound = false;
                    break;
                }
            }
            else if (input == '2') {
                while (true)
                {
                    if (Dealer.CalculaterHandSum() < 17)
                    {
                        Dealer.AddCard(GetRandomCard());
                    }
                    else
                        break;
                }
                DrawUI(Player, Dealer);
                if (Dealer.CalculaterHandSum() > 21)
                {
                    std::cout << "Dealer busted!" << std::endl;
                    Sleep(2000);
                    bRound = false;
                    break;
                }
                if (Dealer.CalculaterHandSum() < Player.CalculaterHandSum())
                {
                    printf("Player wins");
                    Sleep(2000);
                    bRound = false;
                    break;
                }
                else if (Dealer.CalculaterHandSum() > Player.CalculaterHandSum())
                {
                    printf("Dealer wins");
                    Sleep(2000);
                    bRound = false;
                    break;
                }
                else
                {
                    printf("-----|Draw|----");
                    Sleep(2000);
                    bRound = false;
                    break;
                }
            }
        } while (bRound);
        ReShuffleDeck();
    } while (true);
}
