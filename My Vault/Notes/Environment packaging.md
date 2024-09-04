
# Environment packaging


DATE:  24-05-24


Tags: 

# Reference:



## [[Notes/CONDA]]

#### conda create new environment:
conda create --name <environment_name> [python=X.Y.Z] [package1 package2 ...]

#### conda list all environments:
- `conda info --envs`
- `conda env list`






## [[Notes/venv]]

The `venv` module is a built-in module in Python 3.3 and later that allows you to create lightweight virtual environments.

#### venv create new environment:
`python -m venv <environment_name>`

#### Activating the virtual environment:
To activate it on Windows, navigate to the directory containing the `Scripts` folder (usually within the `<environment_name>` directory) and run:

`<environment_name>\Scripts\activate`

#### Deactivating the virtual environment:
`deactivate`

#### venv list all environments:
Unlike `conda`, `venv` itself doesn't provide a built-in command to list all created virtual environments. However, you can find them using your system's directory listing commands.
Look for directories containing `Scripts\activate.bat`:

`dir /s /b /a-d | findstr "Scripts\activate.bat"`

This searches all subdirectories (`/s`) for directories (`/a-d`) and displays only filenames (`/b`), then filters the output using `findstr` to find lines containing `Scripts\activate.bat`.