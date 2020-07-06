# CSAPP-Lab
Practice Labs from the book CSAPP

## DataLab
This project is designed to be run on Unix system. To make it work on Windows, install `Cygwin` and run the code ```make``` to compile the program.  
Installed `Cygwin` packages: `gcc` `g++` etc.  
  
There'll be multiple errors saying that the program cannot find some modules. That's because the program should compile in 32-bit mode in the file `Makefile`, and the 64-bit machine generates conflicts when running the code. To solve this issue, simply delete the '-m32' flag in the line ```CFLAGS = -O -Wall -m32```  
  
Later, run the command ```./btest``` to test the correctness of file `bit.c`.  
Test all functions in a compact form with no error messages: `./btest -g`  
Test function foo for correctness: `./btest -f foo`  
