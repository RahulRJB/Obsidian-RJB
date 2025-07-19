
# uv


DATE:  19-07-25


Tags:  [[Notes/Environment packaging|Environment packaging]]


# References:

https://www.youtube.com/watch?v=AMdG7IjgSPM


# Content:


`uv init` - initialize uv inside a directory

```
uv python install 3.11  - Install a requird python version 
uv python pin 3.11      - pin python to a part version
```  

`uv add <packages>` - add packages / updates to pyproject.toml

`uv remove <packages>` - remove packages

`uv sync`   -  Create a venv and install all the dependencies and packages from pyproject.toml 

`uv run src/analyzer.py` - to run any file. Automatically loads the venv and runs