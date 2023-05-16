IDENTIFICATION DIVISION.
PROGRAM-ID. EMPLOYEE-DATABASE.
       *>Created using GNU COBOL 3.1.2
       *>
       *>
       *>This program creates a list of employees with their first name, last initial, hourly wage, and annual salary.
       *>At the end, the program prints the above info. While I was unable to get subroutines working 
       *>(could not get COBOL working on an IDE) I did include multiple paragraphs that manipulate data as subroutines would.
       *>I also learned how to use COBOL's built-in data structure (tables) and how to accept user input (ACCEPT) to create
       *>a formatted report of some basic employee info.
DATA DIVISION.
    WORKING-STORAGE SECTION.
        01 WS-MAX PIC 9(1).
        *>I used a table to store all of the employee info. I chose to use it because of its flexibility
        *>and it was easy to create multiple data members with different data types.
        01 WS-TABLE.
            05 WS-A PIC X(14) VALUE 'FNAME, L.' OCCURS 5 TIMES.
            05 WS-B PIC 9(2)V9(2) VALUE 00.00 OCCURS 5 TIMES.
            05 WS-C PIC 9(5)V9(2) VALUE 000000.00 OCCURS 5 TIMES.
        01 WS-FNAME PIC A(12).
        01 WS-LNAME PIC A(1).
        01 WS-CNT PIC 9(1).
PROCEDURE DIVISION.

        *>Patent pending
    DISPLAY "WELCOME TO EMPLOYEE-SOFT 78."
    DISPLAY "HOW MANY EMPLOYEES WILL YOU BE ENTERING? YOU MAY ENTER UP TO 5."
    ACCEPT WS-CNT.
        *>I needed a second size variable because I realized that I added everything to the table backwards (2->1->0)
    MOVE WS-CNT TO WS-MAX.
        *>First COBOL loop: PERFORM TIMES
    PERFORM ENTRY-PARA WS-CNT TIMES.
        *>Second COBOL loop: PERFORM (according to: https://www.tutorialspoint.com/cobol/cobol_loop_statements.htm)
    PERFORM SUMMARY-PARA.
        *>Does not actually save the results. This is flavor text for the PERFORM VARYING loop
    DISPLAY "SAVING RESULTS..."
        *>Third COBOL loop: PERFORM VARYING
    PERFORM LAST-LOOP-PARA VARYING WS-CNT FROM 1 BY 1 UNTIL WS-CNT=6.
    STOP RUN.
    
    ENTRY-PARA.
        DISPLAY "PLEASE ENTER THE EMPLOYEE'S FIRST NAME:".
        ACCEPT WS-FNAME.
        DISPLAY "PLEASE ENTER THE EMPLOYEE'S LAST INITIAL:".
        ACCEPT WS-LNAME.
        *>String concatenation 
        STRING WS-FNAME DELIMITED BY SIZE WS-LNAME DELIMITED BY SIZE '. ' DELIMITED BY SIZE INTO WS-A(WS-CNT).
        DISPLAY "WHAT IS " WS-A(WS-CNT) "'S HOURLY WAGE?".
        ACCEPT WS-B(WS-CNT).
        PERFORM SALARY-PARA.
        SUBTRACT 1 FROM WS-CNT GIVING WS-CNT.
        
    SALARY-PARA.
        *>40 hours per week * 52 weeks per year = 2080 hours
        MULTIPLY WS-B(WS-CNT) BY 2080 GIVING WS-C(WS-CNT).
        
    PRINT-PARA.
        *>Prints the list backwards because I (accidentally) put the values into the table backwards
        DISPLAY "EMPLOYEE: " WS-A(WS-MAX) " - WAGE: $" WS-B(WS-MAX) " PER HOUR - GROSS SALARY: $" WS-C(WS-MAX).
        SUBTRACT 1 FROM WS-MAX.
        
    SUMMARY-PARA.
        DISPLAY "YOU HAVE ENTERED " WS-MAX " EMPLOYEE(S). PRINTING REPORT...".
        PERFORM PRINT-PARA UNTIL WS-MAX EQUAL 0.
        
        *>All flavor text
    LAST-LOOP-PARA.
        DISPLAY "(" WS-CNT ")".

        IF WS-CNT = 5 THEN
            DISPLAY "SUCCESS!".
        
        *>The end! 
EXIT.
