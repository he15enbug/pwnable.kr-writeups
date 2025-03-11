# Toddler's Bottle

## [fd]

- There is an executable `fd`, and its source code `fd.c`. And there is another file `flag` that we don't have the permission to read

    ```c
    #include <stdio.h>
    #include <stdlib.h>
    #include <string.h>
    char buf[32];
    int main(int argc, char* argv[], char* envp[]){
            if(argc<2){
                    printf("pass argv[1] a number\n");
                    return 0;
            }
            int fd = atoi( argv[1] ) - 0x1234;
            int len = 0;
            len = read(fd, buf, 32);
            if(!strcmp("LETMEWIN\n", buf)){
                    printf("good job :)\n");
                    system("/bin/cat flag");
                    exit(0);
            }
            printf("learn about Linux file IO\n");
            return 0;

    }
    ```

- From the source we know that we need to pass a number as an argument 
- Then it calls `read(fd, buf, 32)`, where the variable `fd` is `atoi( argv[1] ) - 0x1234`
- If the content being `read` is `LETMEWIN\n`, the program will print out the content of `flag`
- To control the content being `read`, we need to let the program `read` from standard input (`fd` should be `0`, i.e., the argument passed to )
- Run `fd` in this way: `./fd 4660`, then input `LETMEWIN`

## collision


