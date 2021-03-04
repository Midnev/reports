## OpenFrame Sort

### Pre Usage

- install ofcobol

-  install prosort

### Sort Usage

- program call in JCL

        //SORT EXEC PGM=SORT

    the program must be in dir $OPENFRAME_HOME

- input & outputs

        //SORTIN   DD   DSN=SEMINAR.DS03,DISP=SHR
        //SORTOUT  DD   DSN=SEMINAR.DS04,DISP=MOD
        //OF1      DD   DSN=SEMINAR.DS05,DISP=MOD
        //OF2      DD   DSN=SEMINAR.DS06,DISP=MOD

- SYSIN FIELDS=(start,length,Type ...)
    
        //SYSIN    DD   * 
        SORT FIELDS=(11,1,A,13,1,A),FORMAT=CH
        OUTFIL FNAMES=OF1,INCLUDE=(13,1,CH,EQ,C'A')
        OUTFIL FNAMES=OF2,OMIT=(13,1,CH,EQ,C'A')
        /*
    
    In this case sorts 11th 1 length char in Ascending order
  
    then sorts 13th 1 length char in Ascending order
  
        A: Ascending order
        D: Descending order
  
    then pick 13th 1 length char equal to Character A into OF1 
  
    pick except 13th 1 length char equal to Character A into OF2
    
        
        
      
- Full file

        //SEMINAR JOB CLASS=A
        //*********************************************************************
        //INITSTEP EXEC PGM=IDCAMS
        //SYSPRINT DD   SYSOUT=*
        //SYSIN    DD   *
        DELETE (SEMINAR.DS03 SEMINAR.DS04)
        
        DEFINE                            -
         NONVSAM (NAME(SEMINAR.DS03)      -
         VOLUMES(DEFVOL))
        
        DEFINE                            -
         NONVSAM (NAME(SEMINAR.DS04)      -
         VOLUMES(DEFVOL))
        
        DELETE (SEMINAR.DS05 SEMINAR.DS06)
        
        DEFINE                            -
         NONVSAM (NAME(SEMINAR.DS05)      -
         VOLUMES(DEFVOL))
        
        DEFINE                            -
         NONVSAM (NAME(SEMINAR.DS06)      -
         VOLUMES(DEFVOL))
        
        SET MAXCC=0
        /*
        //*********************************************************************
        //*                               STEP2                               *
        //*********************************************************************
        //COPYSTEP EXEC PGM=IEBGENER
        //SETTER   SET  PRMN=SEMINAR
        //SYSPRINT DD   SYSOUT=*
        //SYSIN    DD   DUMMY
        //SYSUT2   DD   DSN=&PRMN..DS03,DISP=MOD
        //SYSUT1   DD   *
        I THINK   3 A
        JCL       2 A
        IS NOT    1 B
        FUN       4 B
        BEF FUN   4 C
        /*
        //*********************************************************************
        //*                               STEP3                               *
        //*********************************************************************
        //SORTSTEP EXEC PGM=SORT
        //SYSPRINT DD   SYSOUT=*
        //SORTIN   DD   DSN=SEMINAR.DS03,DISP=SHR
        //SORTOUT  DD   DSN=SEMINAR.DS04,DISP=MOD
        //OF1      DD   DSN=SEMINAR.DS05,DISP=MOD
        //OF2      DD   DSN=SEMINAR.DS06,DISP=MOD
        //SYSIN    DD   *
        SORT FIELDS=(11,1,A,13,1,A),FORMAT=CH
        
        OUTFIL FNAMES=OF1,INCLUDE=(13,1,CH,EQ,C'A')
        
        OUTFIL FNAMES=OF2,OMIT=(13,1,CH,EQ,C'A')
        /*
        //*********************************************************************
        //*                               STEP4                               *
        //*********************************************************************
        //PRINT EXEC PGM=IDCAMS
        //SYSPRINT DD SYSOUT=*
        //SYSIN DD *
        PRINT INDATASET(SEMINAR.DS05) CHARACTER
        
        PRINT INDATASET(SEMINAR.DS06) CHARACTER
        /*


    

