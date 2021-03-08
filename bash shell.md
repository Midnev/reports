## Bash Shells

### File Structure

- File format {filename}.sh

- File header starts with
        
      #!/bin/bash

    - bash is an interpreter
    
        there are other interpreters and they may use different Syntax


### File Syntax

- set variable
    
      #!/bin/bash
      num1=3

  no spaces on both sides of "=" 


- print string and variable
        
      #!/bin/bash
      num1=3
      echo "Hello World!"
      echo "variable $num1"

- receive parameter or parameters 

      !#/bin/bash
      var1=$1
      var2=$2
      echo "var1 : $var1 , var2 : $var2 "

   is called with parameters like

      ./param.sh 1 2

- basic addition

      #!/bin/bash
      num1=$1
      num2=$2
      sum=$(( num1 + num2 ))
      sum=$(( $sum + 1 ))
      echo "$sum"

- read variables

      #!/bin/bash
      read input
      echo "Given: $input"

- if else (space between "[]" are important)

      #!/bin/bash
      if [ $VAR == "hello" ]
      then
        #todo string cmp
      elif [ $NUM -eq 10]
        #todo : -eq -lt -gt
      else
        #todo
      fi

- loops 
   
   for loop
  
      #!/bin/bash
      for VAR in 1 2 3 4 5 6
      do
       #todo
      done
      =============================
      for VAR in {1..5}
      do
       #todo
      done
      =============================
      for VAR in {0..10..2}
      do
       #todo
      done
      =============================
      for (( i=1 ; i<=5; i++))
      do
       #todo
      done
      =============================
  
   while loop

      #!/bin/bash
      while read line; do
      #todo
      done < filename
      =============================
      while [ $NUM -le 2 ]
      do
      #todo
      done
      =============================
      while [ 1 ]
      do
      #do infinte
      break
      done
      =============================




- call scripts

      #!/bin/bash
      sh ./another.sh

   call and external program and pass cli variables
  
      ./ADDR <<EOF
      $num1
      $num2
      EOF

   in same dir
  
      #!/bin/bash
      res=$(./another.sh)

   call other an external program and get return as a variable
   
      #!/bin/bash
      num1=$1
      num2=$2
      sum=$(./ADDR <<EOF
      $num1
      $num2
      EOF
      )
      echo "From prgm: $sum"


      