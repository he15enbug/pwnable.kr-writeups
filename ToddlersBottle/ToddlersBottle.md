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

- Hash collision
- Source code `col.c`

    ```c
    #include <stdio.h>
    #include <string.h>
    unsigned long hashcode = 0x21DD09EC;
    unsigned long check_password(const char* p){
            int* ip = (int*)p;
            int i;
            int res=0;
            for(i=0; i<5; i++){
                    res += ip[i];
            }
            return res;
    }

    int main(int argc, char* argv[]){
            if(argc<2){
                    printf("usage : %s [passcode]\n", argv[0]);
                    return 0;
            }
            if(strlen(argv[1]) != 20){
                    printf("passcode length should be 20 bytes\n");
                    return 0;
            }

            if(hashcode == check_password( argv[1] )){
                    system("/bin/cat flag");
                    return 0;
            }
            else
                    printf("wrong passcode.\n");
            return 0;
    }
    ```
- From the source code, we know that we need to pass a string of length 20 (in bytes). Then, the `check_password` function will parse our input as 5 integers, and will calculate the sum of them. The flag will be printed out if the sum is `0x21DD09EC` (`568134124`)
- To make the sum be `568134124`, we can use 4 `113626825` (`0X6C5CEC9`) and 1 `113626824` (`0X6C5CEC8`)
- So, we need to pass this to the program: `./col $(printf "\xC8\xCE\xC5\x06\xC9\xCE\xC5\x06\xC9\xCE\xC5\x06\xC9\xCE\xC5\x06\xC9\xCE\xC5\x06")`

## bof


