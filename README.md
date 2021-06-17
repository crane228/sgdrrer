#include <stdio.h>
#include <stdlib.h>

struct sequence {
    char character;
    struct sequence *prev;
};

struct sequence* createElem(char character, struct sequence** root){
    struct sequence* newSeq = (struct sequence*)malloc(sizeof(struct sequence));
    newSeq->character = character;
    newSeq->prev = *root;
    *root = newSeq;
    return newSeq;
};
int popif(char start, struct sequence** root){
    if ((*root)->character == start){
        struct sequence* old = *root;
        *root = (*root)->prev;
        free(old);
        return 1;
    }
    else {
        return 0;
    }
};

int main(int argc, char **argv)
{
    FILE *f = fopen(argv[1], "r");
    if (f == NULL){
        printf("An error has occured while opening file %s", argv[1]);
    }
    else {
        char ch, start1 = NULL, start2 = NULL, start3 = NULL, end1 = NULL, end2 = NULL, end3 = NULL;
        int i = 0;
        do {
            ++i;
        }while (argv[2][i] != '\0');
        if (i >= 2){
            start1 = argv[2][0];
            end1 = argv[2][1];
        }
        if (i >= 4){
            start2 = argv[2][2];
            end2 = argv[2][3];
        }
        if (i >= 6){
            start3 = argv[2][4];
            end3 = argv[2][5];
        }
        int inQuotes = 0, res = 1;
        struct sequence* root = NULL;
        int charCount = 3, lineCount = -1, nCount;
        do {
            ch = fgetc(f);
            ++charCount;
            ++lineCount;
            if (ch == '\n') {
                lineCount = -1;
                nCount = charCount;
            }
            if (ch == '\"'){
                if (inQuotes) inQuotes = 0;
                else inQuotes = 1;
            }
            else if (!inQuotes){
                if (ch == start1 || ch == start2 || ch == start3) createElem(ch, &root);
                else if (ch == end1) res = popif(start1, &root);
                else if (ch == end2) res = popif(start2, &root);
                else if (ch == end3) res = popif(start3, &root);
            }

        }while (ch != -1 && res);
        if (res && root == NULL) printf("That is the way a cookie crumbles");
        else {
            fseek(f, nCount, SEEK_SET);
            while (ch != '\n'){
                ch = fgetc(f);
                printf("%c", ch);
            }
            int j;
            for (j = 0; j < lineCount; ++j){
                printf(" ");
            }
            printf("^\n");
            printf("What cookie?");
        }
    }
    return 0;
}
