Allow executing .py files by using the shebang header
```
#!/usr/bin/python3
```
This would allow running the script via:
```
./script.py [args]
```

Check python version
```
import sys

if sys.version_info[0] < 3:
    raise Exception("Python 3 required")
```
