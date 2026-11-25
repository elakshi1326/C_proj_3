# C_proj_3
/*
 * Tic-Tac-Toe (Two Players) in C
 * Concepts used: 2D arrays, functions, conditions, loops
 * Two players play on a 3x3 board and the program detects the winner or a draw.
 */

#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define SIZE 3

/* ---------- Function declarations ---------- */
void readLine(char *buffer, int size);
int  readInt(void);
void initBoard(char board[SIZE][SIZE]);
void displayBoard(char board[SIZE][SIZE]);
int  placeMark(char board[SIZE][SIZE], int position, char mark);
char checkWinner(char board[SIZE][SIZE]);
int  isBoardFull(char board[SIZE][SIZE]);
void playGame(const char *name1, const char *name2, int scores[3]);

/* ---------- Main ---------- */
int main() {
    char name1[30], name2[30], again[10];
    int scores[3] = {0, 0, 0};   /* [0] = Player 1 wins, [1] = Player 2 wins, [2] = draws */

    printf("====== TIC-TAC-TOE ======\n");

    printf("Enter Player 1 name (X): ");
    readLine(name1, sizeof(name1));
    if (strlen(name1) == 0) strcpy(name1, "Player 1");

    printf("Enter Player 2 name (O): ");
    readLine(name2, sizeof(name2));
    if (strlen(name2) == 0) strcpy(name2, "Player 2");

    do {
        playGame(name1, name2, scores);

        printf("\n--- Scoreboard ---\n");
        printf("%s (X): %d\n", name1, scores[0]);
        printf("%s (O): %d\n", name2, scores[1]);
        printf("Draws    : %d\n", scores[2]);

        printf("\nPlay again? (y/n): ");
        readLine(again, sizeof(again));
    } while (tolower((unsigned char)again[0]) == 'y');

    printf("Thanks for playing. Goodbye!\n");
    return 0;
}

/* ---------- Helper functions ---------- */

/* Reads a line safely and removes the newline */
void readLine(char *buffer, int size) {
    if (fgets(buffer, size, stdin) != NULL)
        buffer[strcspn(buffer, "\n")] = '\0';
    else
        buffer[0] = '\0';
}

/* Reads an integer safely; returns -1 if input is not a number */
int readInt(void) {
    char line[20];
    int value;
    readLine(line, sizeof(line));
    if (sscanf(line, "%d", &value) != 1)
        return -1;
    return value;
}

/* Fills the board with numbers '1' to '9' */
void initBoard(char board[SIZE][SIZE]) {
    int i, j;
    char ch = '1';
    for (i = 0; i < SIZE; i++)
        for (j = 0; j < SIZE; j++)
            board[i][j] = ch++;
}

/* Prints the board */
void displayBoard(char board[SIZE][SIZE]) {
    int i;
    printf("\n");
    for (i = 0; i < SIZE; i++) {
        printf("     %c | %c | %c\n", board[i][0], board[i][1], board[i][2]);
        if (i < SIZE - 1)
            printf("    ---+---+---\n");
    }
    printf("\n");
}

/* Places a mark at the given position (1-9). Returns 1 if successful, 0 if not */
int placeMark(char board[SIZE][SIZE], int position, char mark) {
    int row, col;

    if (position < 1 || position > 9)
        return 0;

    row = (position - 1) / SIZE;
    col = (position - 1) % SIZE;

    /* Cell is taken if it already holds X or O */
    if (board[row][col] == 'X' || board[row][col] == 'O')
        return 0;

    board[row][col] = mark;
    return 1;
}

/* Returns 'X' or 'O' if there is a winner, otherwise ' ' */
char checkWinner(char board[SIZE][SIZE]) {
    int i;

    /* Check rows and columns */
    for (i = 0; i < SIZE; i++) {
        if (board[i][0] == board[i][1] && board[i][1] == board[i][2])
            return board[i][0];
        if (board[0][i] == board[1][i] && board[1][i] == board[2][i])
            return board[0][i];
    }

    /* Check both diagonals */
    if (board[0][0] == board[1][1] && board[1][1] == board[2][2])
        return board[0][0];
    if (board[0][2] == board[1][1] && board[1][1] == board[2][0])
        return board[0][2];

    return ' ';
}

/* Returns 1 if every cell is filled */
int isBoardFull(char board[SIZE][SIZE]) {
    int i, j;
    for (i = 0; i < SIZE; i++)
        for (j = 0; j < SIZE; j++)
            if (board[i][j] != 'X' && board[i][j] != 'O')
                return 0;
    return 1;
}

/* Plays one full game */
void playGame(const char *name1, const char *name2, int scores[3]) {
    char board[SIZE][SIZE];
    int turn = 0;            /* 0 = Player 1 (X), 1 = Player 2 (O) */
    int position;
    char mark, winner = ' ';
    const char *currentName;

    initBoard(board);

    while (1) {
        displayBoard(board);

        currentName = (turn == 0) ? name1 : name2;
        mark        = (turn == 0) ? 'X'   : 'O';

        /* Keep asking until a valid move is made */
        do {
            printf("%s (%c), enter a position (1-9): ", currentName, mark);
            position = readInt();
            if (!placeMark(board, position, mark))
                printf("Invalid move! Choose an empty cell from 1 to 9.\n");
            else
                break;
        } while (1);

        winner = checkWinner(board);
        if (winner != ' ') {
            displayBoard(board);
            printf("*** %s (%c) wins! ***\n", currentName, winner);
            scores[turn]++;
            return;
        }

        if (isBoardFull(board)) {
            displayBoard(board);
            printf("*** It's a draw! ***\n");
            scores[2]++;
            return;
        }

        turn = 1 - turn;   /* switch player */
    }
}