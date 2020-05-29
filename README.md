# CSAPP-Lab
Practice Labs from the book CSAPP

## DataLab
This project is designed to be ran on Unix system. To compile it on Windows system, install `Cygwin` and run the code  
```c
make
```  
There'll be multiple errors saying that the program cannot find some modules. That's because the program should compile in 32-bit mode in the file `Makefile`, and the 64-bit machine generates conflicts when running the code. To solve this issue, simply detele the '-m32' flag in the line ```CFLAGS = -O -Wall -m32```  
Later, run the command  
```./btest```  
to test the correctness of file `bit.c`. 
