# Task1
4 in a row


#define _CRT_SECURE_NO_WARNINGS
#include "stdio.h"
#include "stdlib.h"
#include <stdbool.h>

/*************** Board ***************/
#define ROWS 6
#define COLS 7
char board[ROWS][COLS];


/*********** Declarations ************/

 //This function initializes the game board by assigning each cell
 //with ' ' (resulting with an empty game board).
void initBoard();

//This function gets a row number and a column number (a cell),
//and returns the character in that cell (could be 'X', 'O' or ' ').
//For example:
//char c = getCell(1, 1);
char getCell(int row, int col);

//This function gets a row number, a column number and a sign,
//and assigns the cell with the given sign.
//For example:
//setCell(1, 1, 'X');
void setCell(int row, int col, char sign);
//This function clears the screen.
void clearScreen();

void printCurrentBoard(int playerTurn, int playerColInpt, int row, char playerSymbol, int turnCount, bool finish);
void beginTurn(int playerTurn, int playerColInpt, int row, char playerSymbol, int turnCount, bool finish);
bool checkFour(int playerTurn, char playerSymbol);
void endGame(int playerTurn);


/*************** Main ****************/
int main()
{
	initBoard();
	printCurrentBoard(1, 1, 1, 'X', 1, false);
}


/********** Implementations **********/
char getCell(int row, int col)
{
	return board[7 - row][col];
}

void setCell(int row, int col, char sign)
{
	board[7 - row][col] = sign;
	return;
}

void initBoard()
{
	int i, j;

	for (i = 0; i < ROWS; i++)
	{
		for (j = 0; j < COLS; j++)
		{
			setCell(i + 1, j + 1, ' ');
		}
	}
}

void clearScreen()
{
	system("cls");
}

/***************Prints Board with all inputs entered thus far****************/
void printCurrentBoard(int playerTurn, int playerColInpt, int row, char playerSymbol, int turnCount, bool finish)
{
	for (int i = 0; i < ROWS + 1; i++) {
		if (!i) printf("  ");
		else printf("\n %c", i - 1 + 'A');

		for (int j = 1; j < COLS + 1; j++) {
			if (!i) printf("%d  ", j);
			else printf("%c  ", board[i][j]);

		}
	}

	if (turnCount > ROWS * COLS) { /////whyyyyy???
		printf("\nGame tied.\nNo winner\n\n");
		return;
	}

	if (finish == true)
	{
		endGame(playerTurn);
		return;
	}

	beginTurn(playerTurn, playerColInpt, row, playerSymbol, turnCount, finish);
}


/*************Asks for input, checks for winner, rotates turns, calls printCurrentBoard ()***********/
void beginTurn(int playerTurn, int playerColInpt, int row, char playerSymbol, int turnCount, bool finish)
{

	printf("\nPlayer number %d    %d:\n", playerTurn, turnCount);
	printf("Enter column input (a number between 1-7): ");
	scanf("%d", &playerColInpt);

	//checks if input is between 1-7
	while (playerColInpt > 7) {
		printf("Error: The number you entered is larger than 7.\nPlease try again:\n");
		scanf("%d", &playerColInpt);
	}
	while (playerColInpt < 0) {
		printf("Error: The number you entered is smaller than 0.\nPlease try again:\n");
		scanf("%d", &playerColInpt);
	}

	//if col is full printf ("Error: column is full. please try a different one:\n");
	while (getCell(row, playerColInpt) == 'X' || getCell(row, playerColInpt) == 'O')
	{
		for (int i = 1; i < 7; i++)
		{
			if ((getCell(i, playerColInpt)) != ' ')
				row++;
			//else break;
		}

		if (row == 7) {
			printf("\nColumn is full! Please try one of the other columns.");
			beginTurn(playerTurn, 0 /*playerColInpt*/, 1 /*row*/, playerSymbol, turnCount, finish);
		}

		//else break;
	}

	//sets cell to input after passing eligibility requirements
	setCell(row, playerColInpt, playerSymbol);

	//checks for winner
	if (turnCount > 6) finish = checkFour(playerTurn, playerSymbol);

	if (!finish)
	{
		//rotates turns
		turnCount++;
		if (playerTurn == 1) {
			playerTurn = 2;
			playerSymbol = 'O';
		}
		else {
			playerTurn = 1;
			playerSymbol = 'X';
		}
	}

	clearScreen();

	//prints current board state and moves on to next turn:
	printCurrentBoard(playerTurn, playerColInpt, 1 /*row*/, playerSymbol, turnCount, finish);
}



/*************** checks for a winner of four in a row/column/zig/zag ****************/
bool checkFour(int playerTurn, char playerSymbol)
{

	bool fourRow = false, fourCol = false, fourZig = false, fourZag = false;

	for (int i = 0; i < ROWS + 1; i++) {

		for (int j = 1; j < COLS + 1; j++) {

			//checks 4 in a column
			if ((getCell(i, j) == playerSymbol) && (getCell(i, j) == getCell(i - 1, j)) && (getCell(i - 1, j) == getCell(i - 2, j)) && getCell(i - 2, j) == getCell(i - 3, j)) fourCol = true;

			//checks 4 in a row
			if ((getCell(i, j) == playerSymbol) && (getCell(i, j) == getCell(i, j - 1)) && (getCell(i, j - 1) == getCell(i, j - 2)) && (getCell(i, j - 2) == getCell(i, j - 3))) fourRow = true;

			//checks 4 in a zig
			if ((getCell(i, j) == playerSymbol) && (getCell(i, j) == getCell(i - 1, j - 1)) && (getCell(i - 1, j - 1) == getCell(i - 2, j - 2)) && (getCell(i - 2, j - 2) == getCell(i - 3, j - 3))) fourZig = true;

			//checks 4 in a zag
			if ((getCell(i, j) == playerSymbol) && (getCell(i, j) == getCell(i + 1, j - 1)) && (getCell(i + 1, j - 1) == getCell(i + 2, j - 2)) && (getCell(i + 2, j - 2) == getCell(i + 3, j - 3))) fourZag = true;

		}
	}

	if (fourRow == true || fourCol == true || fourZig == true || fourZag == true)
		return true;
	else
		return false;
}


/*************** Announces winner ****************/
void endGame(int playerTurn)
{
	printf("\nPlayer %d wins! :))\n", playerTurn);
	return;
}
