# Recursive repeats for quick editing of structured text
[fuente](http://vim.wikia.com/wiki/Recursive repeats_for_quick_editing_of_structured_text)

## Grabando un macro Recursivo

Digamos que tenemos las siguientes lineas de de texto

```code
dr-------- 20906
drwx------ 20913
drwxr-x--- 20704
drwxr-xr-x 21104
lrwxrwxrwx 20606
-------r-- 21004
-rw-r----- 20716
-rwxrwx--- 21102
```

Y que por alguna razon queremos mover el numero de 5 digitos, que está al final de la
linea, imediatato al ultimo carater "r" en la misma linea y queremos repetir esto
en el resto del archivo. Notemos que todos esos numeros empiezan con "2" y puede
haber multiples "r" en cada linea.

Este es el resultado de lo que queremos

```code
dr20906--------
dr20913wx------
drwxr20704-x---
drwxr-xr21104-x
lrwxrwxr20606wx
-------r21004--
-rw-r20716-----
-rwxr21102wx---
```

Yes, this can be done via :substitute with back references
(Try: %s/\v(.+r)(.+)\s(\d{5})/\1\3\2/), but that can be intimidating for some.
An easier, more flexible and often quicker method to accomplish the same task is to
use "complex repeats", which really aren't too complex.


Here's how:

Place the cursor on the first character of the first line of the file.
Hit the following keys to record the repeat-sequence:

* qqq : Start and immediately end recording for register q -- this essentially empties register q

* qq : Start recording actions into register q

* f2 : Find the "2" character to the right on this line (cursor will jump there)
* D : Delete and yank the 5-digit number into memory (rest of line)
* Fr : Find the "r" character to the left on this line (cursor will jump to the last "r")

* p : Paste the number that was yanked earlier after the "r" character

* Enter : Move the cursor to the first non-whitespace character of the next line

* @q : Execute the contents of register q (itself!), which is for now, empty,
so nothing will happen. The trick here is that when we run it again with "@q"
after you have ended the recording, it will call itself after processing each
line! This recursive loop will process all the subsequent lines in that file,
until it hits the end-of-file. This is the essence that makes complex repeats
so flexible and powerful.

* q: End the recording

Hit @q to apply the same manipulation to the rest of the file from the 2nd
line onwards. Enjoy the art of flying cursor and automatic text manipulation on
your screen. With this knowledge, you can now quickly perform complex editing of any
structured text with relative ease.

This works much the same way as typing 9999@q to execute the 'q' register as
many times as the count you pass, except that you do not need to think about
the size of your file before doing so to make sure enough 9s are added.
