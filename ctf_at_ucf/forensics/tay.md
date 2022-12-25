A file, ```tay.jpg``` was given, containing a distorted photo of a human face.

We first use the ```strings``` utility to inspect ASCII strings that the image has. We quickly find an interesting string longer than the others:

```ZmxhZ3tuNHoxX3MzeC1yMGIwN30=```

This appears to be in the base64 format. 

**Flag**
```
flag{buffers_and_beef_make_for_a_yummie_pwn_steak}
```
