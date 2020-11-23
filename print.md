# function signature

``` python
print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)
```
Print objects to the text stream file, separated by sep and followed by end. **sep, end, file and flush, if present, must be given as keyword arguments**.    

**All non-keyword arguments are converted to strings like str() does and written to the stream**, separated by sep and followed by end. Both sep and end must be strings; they can also be None, which means to use the default values. If no objects are given, print() will just write end.    

The file argument must be an object with a write(string) method; if it is not present or None, `sys.stdout` will be used. Since printed arguments are converted to text strings, **`print()` cannot be used with *binary mode file objects*. For these, use `file.write(...)` instead**.    

Whether output is buffered is usually determined by file, but if the flush keyword argument.    

> When calling a function, the parentheses is essential, which tell Python to actually execute the function rather than just refer to the function by name.    

> Most programming languages come with a predefined set of escape sequences for special characters such as these:
>
> + `\\`:  backslash
> + `\b`: backspace
> + `\t`: tab
> + `\r`: carriage return (CR)
> + `\n`: newline, also known as line feed (LF)

> `str()` is a global built-in function that converts an object into its string representation.    

`print()` accepts anything regardless of its type. It implicitly calls `str()` behind the scenes to type cast any object into a string. Afterward, it treat strings in a uniform way.    

Despite constant `None` is used to indicate an absence of a value, it will show up as `None` rather than an empty string when you pass it to function `print()`.    

## Separating Multiple Arguments

`print()` can accept any number of *positional arguments*, including zero, one, or more arguments. That's very handy in a common case of message formatting, where you'd want to join a few elements together.    

```python
print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)
```

 The key word `sep` stands for **separator** and is assigned a single space (' ') by default. It determines the value to join elements with. It has to be either a string or `None`.    

> `print(*['jdoe is', 42, 'years old'])`, `*` used to unpack the sequence.    

## Preventing Line Breaks

```python
print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)
```

The keyword `end` dictates what to end the line with.       

+ It must be a string or `None`
+ It can be arbitrarily long
+ It has a default value of `\n`
+ If equal to `None`, it'll have the same effect as the default value
+ If equal to an empty string(''), it will suppress the newline.

> Looping over lines in a text file preserves their own newline characters, which combined with `print()` function's default behavior will result in  a redundant newline character.    
>
> ```python
> with open('file.txt') as fh:
>     for line in fh:
>         print(line)
> ```
>
> ```python
> with open('file.txt') as fh:
>     for line in fh:
>         print(line.rstrip()) # Remove newline character
> ```



## Printing to a File

Believe it or not, `print()` doesn't know how to turn messages into text on your screen, and frankly it doesn't need to. That's a job for lower-level layers of code, which understand bytes and know how to push them around.    

`print()` is an abstraction over these layers, providing a convenient interface that merely delegates the actual printing to a stream or **file-like object**. A stream can be any file on your disk, a network socket, or perhaps an in-memory buffer.    

In addition to this, there are three standard streams provided by the operating system:

1. **stdin**: standard input
2. **stdout**: standard output
3. **stderr**: standard error

> # stream redirection
>
> `$ python hello.py > file.txt`, tell the operating system to temporarily swap out `stdout` for a file stream.    
>
> To redirect `stderr`, you need to know about **file descriptors**, also known as **file handles**.    
>
> | Stream | File Descriptor |
> | ------ | --------------- |
> | stdin  | 0               |
> | stdout | 1               |
> | stderr | 2               |
>
> | Command                       | Description                                      |
> | ----------------------------- | ------------------------------------------------ |
> | ./program > out.txt           | Redirect `stdout`                                |
> | ./program 2> err.txt          | Redirect `stderr`                                |
> | ./program > out.txt 2>err.txt | Redirect `stdout` and `stderr` to separate files |
> | ./program &> out_err.txt      | Redirect `stdout` and `stderr` to the same file  |
>
> In Python, you can access all standard streams though the built-in `sys` module:
>
> ```python
> import sys
> 
> # stdin
> sys.stdin
> sys.stdin.fileno()
> 
> # stdout
> sys.stdout
> sys.stdout.fileno()
> 
> # stderr
> sys.stderr
> sys.stderr.fileno()
> ```

```python
print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)
```

By default, `print()` is bound to `sys.stdout` through its `file` argument. Use the keyword argument `file` to indicate a file that was open in write or append mode, so that messages go straight to it.    

```python
with open('file.txt', mode='w') as fh:
    print('hello world', file=fh)
```

> Don't try using `print()` for writing binary data as it's only well suited for text. Just call the binary file's `.write()` directly.    

## Buffering `print()` Calls



















