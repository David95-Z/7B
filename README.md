#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define SIZE 5

// Function to find and encrypt a pair of characters
void playfairEncrypt(char ch1, char ch2, char key[SIZE][SIZE]) {
    int row1, col1, row2, col2;

    // Find positions of the characters in the key matrix
    int i, j;
    for ( i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            if (key[i][j] == ch1) {
                row1 = i;
                col1 = j;
            }
            if (key[i][j] == ch2) {
                row2 = i;
                col2 = j;
            }
        }
    }

    // Apply Playfair rules
    if (row1 == row2) {
        // Same row: move right
        printf("%c%c", key[row1][(col1 + 1) % SIZE], key[row2][(col2 + 1) % SIZE]);
    } else if (col1 == col2) {
        // Same column: move down
        printf("%c%c", key[(row1 + 1) % SIZE][col1], key[(row2 + 1) % SIZE][col2]);
    } else {
        // Rectangle swap
        printf("%c%c", key[row1][col2], key[row2][col1]);
    }
}

int main() {
    char keyStr[26], text[100], matrix[SIZE][SIZE];
    char alphabet[] = "ABCDEFGHIKLMNOPQRSTUVWXYZ"; // J is merged with I
    char keyTable[25] = {0};
    int used[26] = {0};  // Tracks used letters
    int k = 0;
    int i,j;


    // Get key and plaintext
    printf("Enter key: ");
    scanf("%s", keyStr);

    printf("Enter plaintext: ");
    scanf("%s", text);

    // Convert to uppercase, replace J with I
    for (i = 0; keyStr[i]; i++) {
        char ch = toupper(keyStr[i]);
        if (ch == 'J') ch = 'I';
        if (!used[ch - 'A']) {
            keyTable[k++] = ch;
            used[ch - 'A'] = 1;
        }
    }

    // Fill remaining letters
    for (i = 0; i < 25; i++) {
        char ch = alphabet[i];
        if (!used[ch - 'A']) {
            keyTable[k++] = ch;
            used[ch - 'A'] = 1;
        }
    }

    // Fill the 5x5 matrix
    k = 0;
    printf("\nPlayfair Key Matrix:\n");
    for (i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            matrix[i][j] = keyTable[k++];
            printf("%c ", matrix[i][j]);
        }
        printf("\n");
    }

    // Prepare plaintext: convert to uppercase, replace J with I
    // Insert 'X' between duplicate letters and at the end if needed
    char cleanText[100];
    int len = 0;
    for (i = 0; text[i]; i++) {
        char ch = toupper(text[i]);
        if (ch == 'J') ch = 'I';
        if (isalpha(ch)) {
            cleanText[len++] = ch;
        }
    }
    cleanText[len] = '\0';

    // Adjust plaintext pairs
    char finalText[100];
    int finalLen = 0;
    for (i = 0; i < len; i++) {
        finalText[finalLen++] = cleanText[i];
        if (i + 1 < len && cleanText[i] == cleanText[i + 1]) {
            finalText[finalLen++] = 'X';
        } else if (i + 1 < len) {
            finalText[finalLen++] = cleanText[++i];
        }
    }
    if (finalLen % 2 != 0) {
        finalText[finalLen++] = 'X';
    }
    finalText[finalLen] = '\0';

    // Encrypt
    printf("\nEncrypted Text: ");
    for (i = 0; i < finalLen; i += 2) {
        playfairEncrypt(finalText[i], finalText[i + 1], matrix);
    }

    printf("\n");
    return 0;
}
