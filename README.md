# cd-workshop01
## Aim:
To develop a menu-driven CGPA (Cumulative Grade Point Average) calculator using Lex/Flex and Yacc/Bison that allows users to enter grades and credits for multiple subjects across multiple semesters. The program will compute semester GPA and overall CGPA, providing a practical demonstration of compiler design tools (lexical analysis and parsing) applied to a real-world academic problem
## Algorithm:

1. **Start:** Initialize `semester_points`, `semester_credits`, `total_points`, and `total_credits` to 0.

2. **Display Menu:**
   - `1` → Add subject (grade & credit)
   - `2` → Next semester (compute GPA)
   - `3` → Finish (compute CGPA & exit)

3. **Input Handling (Flex):**
   - Tokenize grades (`O, A+, A, B+, B, C, P, F`)
   - Tokenize credits (numbers)
   - Tokenize menu options (`1, 2, 3`)

4. **Add Subject (MENU1):**
   - Read `grade` and `credit`
   - Convert grade to points using `grade_to_point()`
   - Update `semester_points` and `semester_credits`

5. **Compute Semester GPA (MENU2):**
   - `GPA = semester_points / semester_credits`
   - Add semester totals to cumulative totals (`total_points` and `total_credits`)
   - Reset semester counters (`semester_points` and `semester_credits`)

6. **Compute Final CGPA (MENU3):**
   - `CGPA = total_points / total_credits`
   - Display CGPA and exit program

7. **Error Handling:**
   - Display “syntax error” for invalid or unexpected inputs


Error Handling: Display “syntax error” for invalid inputs.

## Program:
grad.l
```
%{
#include "grad.tab.h"
#include <stdlib.h>
#include <string.h>
%}

%%

O       { yylval.str = strdup("O"); return GRADE; }
A\+     { yylval.str = strdup("A+"); return GRADE; }
A       { yylval.str = strdup("A"); return GRADE; }
B       { yylval.str = strdup("B"); return GRADE; }
C       { yylval.str = strdup("C"); return GRADE; }

[0-9]+  { yylval.num = atoi(yytext); return NUMBER; }

[ \t\n]+ ;  /* skip spaces */
.       { return yytext[0]; }

%%

int yywrap() { return 1; }
```

grad.y
```
%{
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void yyerror(const char *s);
int yylex();

float sem_points = 0, sem_credits = 0;

float grade_to_point(char* g) {
    if (strcmp(g,"O")==0) return 10;
    if (strcmp(g,"A+")==0) return 9;
    if (strcmp(g,"A")==0) return 8;
    if (strcmp(g,"B")==0) return 7;
    if (strcmp(g,"C")==0) return 6;
    return 0;
}
%}

%union {
    char* str;
    int num;
}

%token <str> GRADE
%token <num> NUMBER

%%

input:
      /* empty */
    | input line
;

line:
      GRADE NUMBER {
          float gp = grade_to_point($1);
          sem_points += gp * $2;
          sem_credits += $2;
          printf("Accepted: %s %d\n", $1, $2);
      }
;

%%

void yyerror(const char *s) {
    printf("Error: %s\n", s);
}

int main() {
    printf("Enter grades and credits (Ctrl+D to finish):\n");
    yyparse();
    printf("Semester GPA = %.2f\n", sem_points/sem_credits);
    return 0;
}
```

## Output:
<img width="657" height="236" alt="image" src="https://github.com/user-attachments/assets/120724d0-82ec-47ac-92ab-14c74aa19111" />

## Result:

The CGPA calculator computes semester GPA and final CGPA based on user-entered grades and credits.
