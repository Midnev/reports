

### Question1 

- 1 
  
  case1:

      01 TEST01.
         03 TEST1-KEY PIC 9(7) COMP-3.
         03 TEST1-DATA PIC X(6).
         03 TEST1-RD REDEFINES TEST1-DATA PIC X(6).

  case2:
    
      01 TEST01.
        03 TEST1-KEY PIC 9(7) COMP-3.
        03 TEST1-DATA PIC X(6).
      01 TEST1-RD REDEFINES TEST01 PIC X(10).

- 2 

      00000013414243444546
      000000134A4B4C4D4E

- 3 

  - create a directory under $OPENFRAME_HOME directory
    
        mkdir newvolume

  - edit config files
    
    volume.conf
        
        [VOLUME]
            ...
            NEWVOL=0003,PUBLIC,PERMANENT,$OPENFRAME_HOME/newvolume
        
    unit.conf
    
        [DEVICE]
            ...
            0003=1,3380,2048

  - define volume
    
        voladd define NEWVOL $OPENFRAME_HOME/newvolume

  - create copy book in dir

        cd $OPENFRAME_HOME/tsam/copybook
        vi COPY.BOOK.cpy

  - idcams 
    
         idcams define -t CL -n COPY.BOOK -k4,0 -l 10,10

### Question2

  - 1

        //TEST JOB CLASS=B
        //*--------------------------------------------------------------------
        // EXEC PGM=IDCAMS
        //SYSOUT DD SYSOUT=*
        //SYSIN DD *
        DELETE (TEST.INPUT.DATASET -
        TEST.OUTPUT.DATASET        -
        TEST.FINAL.DATASET)
        SET MAXCC=0
        /*
        //*--------------------------------------------------------------------
        // EXEC PGM=IEBGENER
        //SYSOUT DD SYSOUT=*
        //SYSIN DD DUMMY
        //SYSUT2 DD DSN=TEST.INPUT.DATASET,DISP=(,CATLG,DELETE),
        // DCB=(RECFM=FB,LRECL=80)
        //SYSUT1 DD *
        0002 D 338
        0004 C 2ND
        0006 X 393238
        0005 D 928
        0001 C 1ST
        0003 X 333338
        //*--------------------------------------------------------------------
        // EXEC PGM=SORT
        //SYSOUT DD SYSOUT=*
        //SORTIN DD DSN=TEST.INPUT.DATASET,DISP=SHR
        //SORTOUT DD DSN=TEST.OUTPUT.DATASET,DISP=(,CATLG,DELETE)
        //SYSIN DD *
          SORT FIELDS=(6,1,A),FORMAT=CH
        /*
        //*--------------------------------------------------------------------
        // EXEC PGM=TESTCOB
        //SYSOUT DD SYSOUT=*
        //INDD DD DSN=TEST.OUTPUT.DATASET,DISP=SHR
        //OUTDD DD  DSN=TEST.FINAL.DATASET,DISP=(,CATLG,DELETE)
        //

 - 2 

              ID DIVISION.
                PROGRAM-ID. TESTCOB.
               ENVIRONMENT DIVISION.
                INPUT-OUTPUT SECTION.
                  FILE-CONTROL.
                   SELECT INPUT-FD ASSIGN INDD.
                   SELECT OUTPUT-FD ASSIGN OUTDD.
               DATA DIVISION.
                FILE SECTION.
                  FD INPUT-FD.
                    01 INPUT-REC.
                      03 R-KEY1 PIC 9(04).
                      03 FILLER PIC X(01).
                      03 R-KEY2 PIC X(01).
                      03 FILLER PIC X(01).
                      03 R-DATA PIC X(06).
                      03 FILLER PIC X(67).
                    01 INPUT-REC-R REDEFINES INPUT-REC
                                            PIC X(80).
                  FD OUTPUT-FD.
                    01 OUTPUT-REC.
                      03 OUT-KEY   PIC 9(8).
                      03 FILLER2   PIC X(2) VALUE ': '.
                      03 OUT-KEY2  PIC X(1).
                      03 FILLER    PIC X(1).
                      03 OUT-DATA  PIC X(6).
                      03 FILLER    PIC X(62).
                WORKING-STORAGE SECTION.
                    01 INPUT-EOF PIC X(01) VALUE 'N'.
                      88 INPUT-EOF-Y VALUE 'Y'.
                      88 INPUT-EOF-N VALUE 'N'.
            
               PROCEDURE DIVISION.
                 0000-MAIN.
                   PERFORM 0001-INIT THRU 0001-INIT-EXIT.
                   PERFORM 0002-READ 6 TIMES.
                 0000-MAIN-EXIT.
                   PERFORM 9999-RETURN.
                 0001-INIT.
                   OPEN INPUT INPUT-FD.
                   OPEN EXTEND OUTPUT-FD.
                 0001-INIT-EXIT.
                   EXIT.
                 0002-READ.
                   READ INPUT-FD
                    AT END MOVE 'Y' TO INPUT-EOF.
                    IF INPUT-EOF-N
                      DISPLAY INPUT-REC.
                    PERFORM 0003-WRITE.
                 0002-READ-EXIT.
                   EXIT.
                 0003-WRITE.
                    MOVE R-KEY1 TO OUT-KEY.
                    MOVE R-KEY2 TO OUT-KEY2.
                    MOVE R-DATA TO OUT-DATA.
                    WRITE OUTPUT-REC.
                 0003-WRITE-EXIT.
                   EXIT.
                 9999-RETURN.
                   CLOSE OUTPUT-FD.
                   CLOSE INPUT-FD.
                   STOP RUN.

  - 