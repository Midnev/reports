## Cobol & JCL

### Objective : Basic Adding program

- Addition program

      =======(Space)
             ID DIVISION.
              PROGRAM-ID ADDR.
             ENVIRONMENT DIVISION.
             DATA DIVISION.
              WORKING-STORAGE SECTION.
               01 NUM1 PIC 9(2).
               01 NUM2 PIC 9(2).
               01 RESULT PIC 9(3).
             PROCEDURE DIVISION.
              DISPLAY "ADDER START".
              ACCEPT NUM1.
              ACCEPT NUM2.
              ADD NUM1 TO NUM2 GIVING RESULT.
              DISPLAY "[" RESULT "]".
             EXIT PROGRAM.


- Compile

        ofcob addr.cob -o  ADDR
  Openframe util dir specification for program

        $OPENFRAME_HOME/util

- JCL Calls

        //STA1 JOB CLASS=A,MSGLEVEL=(2,1) COMMENTS HERE
        //ADDRSTEP EXEC PGM=ADDR
        //SYSPRINT DD SYSOUT=*
        //SYSIN DD *
        4
        5
        /*

    Run a job  
  
        textrun -j{filedir}

### Objective : NONVSAM ACCESS
        
- Main Program

      =======(Space)
             ID DIVISION.
               PROGRAM-ID FRW.
               ENVIRONMENT DIVISION.
                CONFIGURATION SECTION.
                INPUT-OUTPUT SECTION.
                 FILE-CONTROL.
                  SELECT F-REC ASSIGN FIN.
                  SELECT F-OUT ASSIGN FOUT.
             DATA DIVISION.
              FILE SECTION.
               FD F-REC.
                01 FIELD.
                 02 FIELD-KEY PIC  9999.
                 02 FIELD-VAR PIC  X(26).
                 02 FIELD-VA2 PIC  X(20).
                 02 FIELD-EMT PIC  X(30).
               FD F-OUT.
                01 OFIELD. 
                 02 OFIELD-K  PIC 9999.
                 02 OFIELD-V1 PIC X(20).
                 02 OFIELD-V2 PIC X(30).
                 02 OFIELD-EM PIC X(26).
              WORKING-STORAGE SECTION.
             PROCEDURE DIVISION.
              DISPLAY "FILE R/W".
              OPEN INPUT F-REC.
              OPEN EXTEND F-OUT.
               PERFORM 5 TIMES
                READ F-REC
                 MOVE FIELD-KEY TO OFIELD-K
                 MOVE FIELD-VAR TO OFIELD-EM
                 MOVE FIELD-VA2 TO OFIELD-V1
                 MOVE FIELD-EMT TO OFIELD-V2
                WRITE OFIELD
                DISPLAY OFIELD
               END-PERFORM.
              CLOSE F-OUT.
              CLOSE F-REC.
              DISPLAY "PGM END".
             EXIT PROGRAM.


- compile

      sofcob fRW.cob -o FRW -L$OPENFRAME_HOME/lib -ltextfh
  Openframe util dir specification for program

      $OPENFRAME_HOME/util

- JCL Call

      //FILEC JOB CLASS=A
      //*********************************************************************
      //INITSTEP EXEC PGM=IDCAMS
      //SYSPRINT DD   SYSOUT=*
      //SYSIN    DD   *
      DELETE (TEST.DS01 TEST.DS02)
      
      DEFINE                            -
          NONVSAM (NAME(TEST.DS01)      -
          VOLUMES(DEFVOL))
      DEFINE                            -
          NONVSAM (NAME(TEST.DS02)      -
          VOLUMES(DEFVOL))
      
      SET MAXCC=0
      /*
      //*********************************************************************
      //*                               STEP2                               *
      //*********************************************************************
      //COPYSTEP EXEC PGM=IEBGENER
      //SETTER   SET  PRMN=TEST
      //SYSPRINT DD   SYSOUT=*
      //SYSIN    DD   DUMMY
      //SYSUT2   DD   DSN=&PRMN..DS01,DISP=MOD
      //SYSUT1   DD   *
      1ADCBEFGHIJKLMNOPQRSTUVWXYZ1234567890
      2ADCBEFGHIJKLMNOPQRSTUVWXYZ1234567890
      3ADCBEFGHIJKLMNOPQRSTUVWXYZ1234567890
      4ADCBEFGHIJKLMNOPQRSTUVWXYZ1234567890
      5ADCBEFGHIJKLMNOPQRSTUVWXYZ1234567890
      6ADCBEFGHIJKLMNOPQRSTUVWXYZ1234567890
      /*
      //*********************************************************************
      //*                               STEP3                               *
      //*********************************************************************
      //FSTEP EXEC PGM=FR
      //SYSPRINT DD   SYSOUT=*
      //FIN   DD   DSN=TEST.DS01,DISP=MOD
      //FOUT  DD   DSN=TEST.DS02,DISP=MOD
      //*********************************************************************
      //*                               STEP4                               *
      //*********************************************************************
      //PRINT EXEC PGM=IDCAMS
      //SYSPRINT DD SYSOUT=*
      //SYSIN DD *
      PRINT INDATASET(TEST.DS02) CHARACTER
      /*




### Objective : VSAM ACCESS

- Create a copybook 

             01 F-IN.
              02 I-KEY        PIC    9(4).
              02 I-DATA       PIC    X(26).
              02 I-COMMENT    PIC    X(30).
              02 I-SEQ.
               03 I-SEQ-NUM  PIC    9(4).
               03 I-SEQ-TYPE PIC    X(16).

- Main Program
  
      =======(Space)
             ID DIVISION.
              PROGRAM-ID VFRW.
             ENVIRONMENT DIVISION.
              CONFIGURATION SECTION.
              INPUT-OUTPUT SECTION.
               FILE-CONTROL.
                SELECT INFILE ASSIGN FIN
                 ORGANIZATION IS INDEXED
                 ACCESS MODE IS SEQUENTIAL
                 RECORD KEY IS OFIELD-K.
                SELECT OUTFILE ASSIGN FOUT.
             DATA DIVISION.
              FILE SECTION.
               FD INFILE.
                01 F-IN.
                 02 I-KEY        PIC    9(4).
                 02 I-DATA       PIC    X(26).
                 02 I-COMMENT    PIC    X(30).
                 02 I-SEQ.
                  03 I-SEQ-NUM  PIC    9(4).
                  03 I-SEQ-TYPE PIC    X(16).
                FD OUTFILE.
                01 F-OUT.
                 02 O-KEY        PIC    9(4).
                 02 O-STR        PIC   X(26).
                 02 O-CMT        PIC   X(30).
                 02 O-DT.
                  03 O-DT-NUM    PIC   9(4) COMP-3.
                  03 O-DT-TYPE    PIC   X(16).
              WORKING-STORAGE SECTION.
  
             PROCEDURE DIVISION.
              DISPLAY "FILE R/W".
              OPEN INPUT F-REC.
              OPEN EXTEND F-OUT.
               PERFORM 5 TIMES
                READ INFILE
                  MOVE I-KEY     TO O-KEY
                  MOVE I-DATA    TO O-STR
                  MOVE I-COMMENT TO O-CMT
                  MOVE I-SEQ-NUM TO O-DT-NUM
                  DISPLAY "[" O-DT-NUM "]"
                  MOVE I-SEQ-TYPE TO O-DT-TYPE
                WRITE F-OUT
               END-PERFORM.
              CLOSE F-OUT.
              CLOSE F-REC.
              DISPLAY "PGM END".
             EXIT PROGRAM.

- compile

        ofcob vfrw.cob -o VFRW -L$OPENFRAME_HOME/lib -ltextfh

  Openframe Util dir specification for program

        $OPENFRAME_HOME/util

- JCL call

      //********** ********** ********** ********** **********
      //VSA JOB CLASS=A
      //*
      //INITSTEP EXEC PGM=IDCAMS
      //SYSPRINT DD   SYSOUT=*
      //SYSIN    DD   *
      DELETE (TESTS.DS01 TESTS.DS02)
      
      DEFINE CLUSTER                    -
                 (NAME(TESTS.DS01)      -
          RECORDSIZE(80, 80)            -
          CYLINDERS(3,2)                -
          KEYS(4,0)                     -
          VOLUMES(NEWVOL)               -
          INDEXED)                      -
         INDEX (NAME(TESTS.DS01.INDEX)) -
      DATA (NAME(TESTS.DS01.DATA))
      
      DEFINE                            -
          NONVSAM (NAME(TESTS.DS02)     -
          VOLUMES(NEWVOL))
      
      SET MAXCC=0
      /*
      //********** ********** ********** ********** **********
      //COPYSTEP EXEC PGM=IEBGENER
      //SYSPRINT DD   SYSOUT=*
      //SYSIN    DD   DUMMY
      //SYSUT2   DD   DSN=TESTS.DS01,DISP=MOD
      //SYSUT1   DD   *
      1   DATA11   t         t      DATA12             tEMT1     ]0565 A
      2   DATA21                    DATA22              EMT2     ]0677 B
      3   DATA31                    DATA32              EMT3     ]0783 C
      4   DATA41                    DATA42              EMT4     ]0892 D
      5   DATA51                    DATA52              EMT5     ]0901 E
      6   DATA61                    DATA62              EMT6     ]0012 F
      /*
      //********** ********** ********** ********** **********
      //VSSTEP EXEC PGM=VFRW
      //SYSPRINT DD   SYSOUT=*
      //FIN      DD   DSN=TESTS.DS01,DISP=SHR
      //FOUT     DD   DSN=TESTS.DS02,DISP=MOD
      //********** ********** ********** ********** **********
      //PRINT EXEC PGM=IDCAMS
      //SYSPRINT DD SYSOUT=*
      //SYSIN DD *
      PRINT INDATASET(TESTS.DS02) CHARACTER
      /*

        


### Objective : create a cobol progam to call external program

- Main Program : CALLER
  
      =======(Space)
             ID DIVISION.
              PROGRAM-ID   CALLER.
             ENVIRONMENT DIVISION.
             DATA DIVISION.
              WORKING-STORAGE SECTION.
               01 NUM1 PIC 9(2).
        
             PROCEDURE DIVISION.
              DISPLAY "CALLER START".
              ACCEPT NUM1.
              CALL 'CALLEE' USING NUM1.
              DISPLAY "RESULT: [" NUM1 "]".
             EXIT PROGRAM.


- Sub Program : CALLEE

      =======(Space)
             ID DIVISION.
              PROGRAM-ID CALLEE.
             ENVIRONMENT DIVISION.
             DATA DIVISION.
              LINKAGE SECTION.
               01 NUM1 PIC 9(2).
              WORKING-STORAGE SECTION.
               01 RES PIC 9(2).
             PROCEDURE DIVISION USING NUM1.
              DISPLAY "CALLEE START".
              DISPLAY "CALLED: [" NUM1 "]".
              ADD NUM1 TO 1 GIVING RES.
              MOVE RES TO NUM1.
              DISPLAY "MODIFIED: [" NUM1 "]".
             EXIT PROGRAM.

- Compile
  
        ofcob caller.cob -o CALLER.so
        ofcob callee.cob -o CALLEE.so

    When making an executable 
  
        ofcob caller.cob -o CALLER -x

- JCL calls
    
    Openframe util dir specification for CALLER.so
  
        $OPENFRAME_HOME/util
        
    OpenFrame USERLIB Specification for CALLEE.so
        
        $OPENFRAME_HOME/volume_default/SYS1.USERLIB
  
    JCL File
  
        //CL JOB CLASS=A
        //*********************************************************************
        //*                               STEP1                               *
        //*********************************************************************
        //CSSTEP   EXEC PGM=CALLER
        //STEPLIB  DD   DSN=SYS1.USERLIB,DISP=SHR
        //SYSPRINT DD   SYSOUT=*
        //SYSIN    DD   *
        11
        /*

        
