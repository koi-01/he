#### OP PRECEDENCE
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>  // For isdigit()

#define MAX 100

// --------------------------------------------------------------
// [1] : Operator Precedence Table
// --------------------------------------------------------------
// This table defines precedence relationships between operators.
// '>': Top of stack operator has higher precedence (Reduce)
// '<': Input operator has higher precedence (Shift)
// '=': Equal precedence (Shift)
// 'A': Accept (End of parsing)

char precedence_table[5][5] = {
    //  +    -    *    /    $
    {'>', '>', '<', '<', '>'}, // +
    {'>', '>', '<', '<', '>'}, // -
    {'>', '>', '>', '>', '>'}, // *
    {'>', '>', '>', '>', '>'}, // /
    {'<', '<', '<', '<', 'A'}  // $
};
char operators[] = "+-*/$";

// Returns the index of the operator in the precedence table.
// If the character is not an operator, returns -1.

int getIndex(char symbol) {
    for (int i = 0; i < 5; i++)
        if (operators[i] == symbol)
            return i;
    return -1;
}

// Defines a simple stack for parsing.
typedef struct {
    char stack[MAX];
    int top;
} Stack;

// Stack functions
void push(Stack *s, char symbol) {
    s->stack[++(s->top)] = symbol;
}
char pop(Stack *s) {
    return s->stack[(s->top)--];
}
char peek(Stack *s) {
    return s->stack[s->top];
}
int isOperator(char c) {
    return (c == '+' || c == '-' || c == '*' || c == '/' || c == '$');
}
void parseExpression(char *input) {
    Stack stack;
    stack.top = -1;
    push(&stack, '$'); // Push end marker
    int i = 0;

    printf("Stack\tInput\tAction\n");

    while (1) {
        // Skip spaces
        while (input[i] == ' ') i++;

        // Print stack content
        for (int j = 0; j <= stack.top; j++)
            printf("%c", stack.stack[j]);
        printf("\t%s\t", &input[i]);

        char top_symbol = peek(&stack);
        char next_symbol = input[i];

        // If the character is a number or variable, we shift.
        if (isdigit(next_symbol) || isalpha(next_symbol)) {
            printf("Shift (Operand: %c)\n", next_symbol);
            i++;
            while (isdigit(input[i]) || isalpha(input[i])) i++; // Skip full operand
            continue;
        }
        int top_index = getIndex(top_symbol);
        int next_index = getIndex(next_symbol);

        if (top_index == -1 || next_index == -1) {
            printf("Error: Invalid character in input\n");
            return;
        }
        char precedence = precedence_table[top_index][next_index];
        if (precedence == '<' || precedence == '=') {
            push(&stack, next_symbol);
            printf("Shift\n");
            i++;
        } 
        else if (precedence == '>') {
            while (getIndex(peek(&stack)) > getIndex(next_symbol)) {
                pop(&stack);
            }
            printf("Reduce\n");
        } 
        else if (precedence == 'A' && peek(&stack) == '$' && next_symbol == '$') {
            printf("Input accepted!\n");
            return;
        } 
        else {
            printf("Error: Invalid precedence relation\n");
            return;
        }
    }
}
int main() {
    char input[MAX];
    printf("Enter expression (end with $): ");
    fgets(input, MAX, stdin);  // Use fgets to handle spaces
    parseExpression(input);
    return 0;
```

#### SR
```
#include <bits/stdc++.h>
using namespace std;
int z = 0, i = 0, j = 0, c = 0;
char a[16], ac[20], stk[15], act[10];

void check() {
    strcpy(ac, "REDUCE TO E -> ");
    
    for(z = 0; z < c; z++) {
        if(stk[z] == '4') {
            printf("%s4", ac);
            stk[z] = 'E';
            stk[z + 1] = '\0';
            printf("\n$%s\t%s$\t", stk, a);
        }
    }
    for(z = 0; z < c - 2; z++) {
        if(stk[z] == '2' && stk[z + 1] == 'E' && stk[z + 2] == '2') {
            printf("%s2E2", ac);
            stk[z] = 'E';
            stk[z + 1] = '\0';
            stk[z + 2] = '\0';
            printf("\n$%s\t%s$\t", stk, a);
            i = i - 2;
        }
    }
    for(z = 0; z < c - 2; z++) {
        if(stk[z] == '3' && stk[z + 1] == 'E' && stk[z + 2] == '3') {
            printf("%s3E3", ac);
            stk[z] = 'E';
            stk[z + 1] = '\0';
            stk[z + 2] = '\0';
            printf("\n$%s\t%s$\t", stk, a);
            i = i - 2;
        }
    }
    return;
}

int main() {
    strcpy(a, "32423");
    c = strlen(a);
    strcpy(act, "SHIFT");
    
    printf("\nstack \t input \t action");
    printf("\n$\t%s$\t", a);

    for(i = 0; j < c; i++, j++) {
        printf("%s", act);
        stk[i] = a[j];
        stk[i + 1] = '\0';
        a[j] = ' ';
        printf("\n$%s\t%s$\t", stk, a);
        check();
    }
    check();
    
    if(stk[0] == 'E' && stk[1] == '\0')
        printf("Accept\n");
    else
        printf("Reject\n");
}
```

#### LR
```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

//---------------------------------------------------
// {1} : Stack Implementation for Parsing
//---------------------------------------------------

char stack[30];
int top = -1;

// Function to push a character onto the stack
void push(char c) {
    top++;
    stack[top] = c;
}

// Function to pop a character from the stack
char pop() {
    char c;
    if (top != -1) {
        c = stack[top];
        top--;
        return c;
    }
    return 'x'; // Return 'x' if the stack is empty
}

// Function to print the current status of the stack
void printstat() {
    int i;
    printf("\n\t\t\t $");
    for (i = 0; i <= top; i++)
        printf("%c", stack[i]);
}

//---------------------------------------------------
// {2} : Main Function to Simulate LR Parsing
//---------------------------------------------------

int main() {
    int i, l;
    char s1[20], ch1, ch2, ch3;

    // Prompt for input
    printf("\n\n\t\t LR PARSING");
    printf("\n\t\t ENTER THE EXPRESSION: ");
    scanf("%s", s1);
    l = strlen(s1);

    printf("\n\t\t $");

    //---------------------------------------------------
    // {3} : Token Processing Loop
    //      Convert "id" to 'E' and push operators
    //---------------------------------------------------

    for (i = 0; i < l; i++) {
        if (s1[i] == 'i' && s1[i + 1] == 'd') { // Detect 'id'
            s1[i] = ' ';         // Mark 'i' as processed
            s1[i + 1] = 'E';     // Replace 'd' with 'E' (non-terminal)
            
            printstat();
            printf(" id");

            push('E');           // Push non-terminal onto stack
            printstat();
        }
        else if (s1[i] == '+' || s1[i] == '-' || s1[i] == '*' || s1[i] == '/') {
            push(s1[i]);         // Push operator onto stack
            printstat();
        }
    }

    //---------------------------------------------------
    // {4} : Reduction Loop
    //      Reduce expressions in stack (E op E -> E)
    //---------------------------------------------------

    printstat();

    while (top >= 0) {
        ch1 = pop();             // Pop the top of the stack

        if (ch1 == 'x') {        // If stack is empty, break
            printf("\n\t\t\t $");
            break;
        }

        if (ch1 == '+' || ch1 == '/' || ch1 == '*' || ch1 == '-') {
            ch3 = pop();         // Expecting an 'E' after operator

            if (ch3 != 'E') {
                printf("error"); // Invalid reduction
                exit(1);
            }
            else {
                push('E');       // Successful reduction, push back 'E'
                printstat();
            }
        }

        ch2 = ch1;               // Store current character
    }
}
```

#### SYMBOL TABLE
```
#include <stdio.h>
#include <ctype.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>

// -------------------------------------------------------
// [1] : Main Function - Builds a Symbol Table from Input Expression
// -------------------------------------------------------
int main() {
    int i = 0, j = 0, x = 0, n;
    void *p, *add[5];
    char ch, srch, b[15], d[15], c;

    printf("Expression terminated by $: ");

    // -------------------------------------------------------
    // [2] : Read Expression Until '$'
    // -------------------------------------------------------
    while ((c = getchar()) != '$') {
        b[i] = c;
        i++;
    }

    n = i - 1;

    // -------------------------------------------------------
    // [3] : Print the Given Expression
    // -------------------------------------------------------
    printf("Given Expression: ");
    i = 0;
    while (i <= n) {
        printf("%c", b[i]);
        i++;
    }

    // -------------------------------------------------------
    // [4] : Construct and Display Symbol Table
    // -------------------------------------------------------
    printf("\nSymbol Table\n");
    printf("Symbol \t addr \t type");

    while (j <= n) {
        c = b[j];

        if (isalpha(toascii(c))) {
            // Identifier
            p = malloc(sizeof(char));
            add[x] = p;
            d[x] = c;
            printf("\n%c \t %p \t identifier", c, p);
            x++;
        } else if (c == '+' || c == '-' || c == '*' || c == '=') {
            // Operator
            p = malloc(sizeof(char));
            add[x] = p;
            d[x] = c;
            printf("\n%c \t %p \t operator", c, p);
            x++;
        }
        j++;
    }

    return 0;
}
```

#### INTERMEDIATE + 3-ADDRESS
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

#define MAX 100

//------------------------------------------------[2]: Global Declarations

char expr[MAX];              // Input expression
int tempVarCount = 1;        // Temporary variable counter (t1, t2, ...)

char op[MAX];                // Operator stack
int opTop = -1;

char operand[MAX][10];       // Operand stack (holds variables or temp results)
int operandTop = -1;

typedef struct {
    char result[10];         // Temporary result variable (t1, t2...)
    char operand1[10];       // Left-hand side operand
    char operand2[10];       // Right-hand side operand
    char op;                 // Operator (+, -, *, /)
} TAC;

TAC tacList[MAX];            // Array to hold all TAC instructions
int tacIndex = 0;            // Current number of TAC instructions

int precedence(char);
void generateTAC();
void generateAssembly();
void pushOperator(char);
char popOperator();
void pushOperand(char *);
char *popOperand();

int main() {
    printf("Enter an arithmetic expression: ");
    scanf("%s", expr);

    printf("\nThree Address Code (TAC):\n");
    generateTAC();

    printf("\nEquivalent Assembly Code:\n");
    generateAssembly();

    return 0;
}

int precedence(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    return 0;
}

void generateTAC() {
    char temp[10];
    int i, j = 0, length = strlen(expr);

    for (i = 0; i < length; i++) {
        if (isalnum(expr[i])) {
            // Build full operand (e.g., a, b, x1)
            temp[j++] = expr[i];
            if (i == length - 1 || !isalnum(expr[i + 1])) {
                temp[j] = '\0';
                pushOperand(temp);
                j = 0;
            }
        } 
        else if (expr[i] == '(') {
            pushOperator(expr[i]);
        } 
        else if (expr[i] == ')') {
            while (opTop >= 0 && op[opTop] != '(') {
                char oper = popOperator();
                char *right = popOperand();
                char *left = popOperand();

                sprintf(tacList[tacIndex].result, "t%d", tempVarCount);
                strcpy(tacList[tacIndex].operand1, left);
                strcpy(tacList[tacIndex].operand2, right);
                tacList[tacIndex].op = oper;
                tacIndex++;

                char tempVarStr[10];
                sprintf(tempVarStr, "t%d", tempVarCount++);
                pushOperand(tempVarStr);
            }
            popOperator(); // Pop '('
        } 
        else {
            while (opTop >= 0 && precedence(op[opTop]) >= precedence(expr[i])) {
                char oper = popOperator();
                char *right = popOperand();
                char *left = popOperand();

                sprintf(tacList[tacIndex].result, "t%d", tempVarCount);
                strcpy(tacList[tacIndex].operand1, left);
                strcpy(tacList[tacIndex].operand2, right);
                tacList[tacIndex].op = oper;
                tacIndex++;

                char tempVarStr[10];
                sprintf(tempVarStr, "t%d", tempVarCount++);
                pushOperand(tempVarStr);
            }
            pushOperator(expr[i]);
        }
    }

    // Remaining operators
    while (opTop >= 0) {
        char oper = popOperator();
        char *right = popOperand();
        char *left = popOperand();

        sprintf(tacList[tacIndex].result, "t%d", tempVarCount);
        strcpy(tacList[tacIndex].operand1, left);
        strcpy(tacList[tacIndex].operand2, right);
        tacList[tacIndex].op = oper;
        tacIndex++;

        char tempVarStr[10];
        sprintf(tempVarStr, "t%d", tempVarCount++);
        pushOperand(tempVarStr);
    }

    // Print TAC
    for (i = 0; i < tacIndex; i++) {
        printf("  %s := %s %c %s\n", tacList[i].result, tacList[i].operand1, tacList[i].op, tacList[i].operand2);
    }
}

void generateAssembly() {
    for (int i = 0; i < tacIndex; i++) {
        printf("  MOV R0, %s\n", tacList[i].operand1);
        switch (tacList[i].op) {
            case '+': printf("  ADD R0, %s\n", tacList[i].operand2); break;
            case '-': printf("  SUB R0, %s\n", tacList[i].operand2); break;
            case '*': printf("  MUL R0, %s\n", tacList[i].operand2); break;
            case '/': printf("  DIV R0, %s\n", tacList[i].operand2); break;
        }
        printf("  MOV %s, R0\n", tacList[i].result);
    }
}
void pushOperator(char opr) {
    op[++opTop] = opr;
}
char popOperator() {
    return op[opTop--];
}
void pushOperand(char *val) {
    strcpy(operand[++operandTop], val);
}
char *popOperand() {
    return operand[operandTop--];
}
```

#### LEXICAL 
```
#include <iostream>
using namespace std;

int main() {
    int num = 10;
    int @value = 20; 
    // Lexical error: '@' is not a valid character in identifier names

    cout << num + @value << endl;
    return 0;
}
```

####  Syntax 
```
#include <iostream>
using namespace std;

int main() {
    int x = 10
    cout << "Value of x is: " << x << endl;
    // Missing semicolon ; after int x = 10 leads to a syntax error
    return 0;
}
```

#### Semantic 
```
#include <iostream>
using namespace std;

int main() {
    int totalApples = 10;
    int totalOranges = 5;

    int totalFruits = totalApples - totalOranges; 
// Semantic error: Wrong logic (should be addition)

    cout << "Total fruits: " << totalFruits << endl;
    return 0;
}
```
