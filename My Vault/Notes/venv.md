
# venv


DATE:  10-06-25

 
Tags:  [[Notes/Environment packaging|Environment packaging]]

# References:




# Content:

The `venv` module comes built into Python 3.3 and later, so you don't need to install anything extra. It creates lightweight virtual environments that are essentially directories containing their own Python interpreter and package installation space.

## Creating Your First Virtual Environment

Let's start with the basic command to create a virtual environment. Navigate to your project directory in your terminal and run:


```
python -m venv myproject_env
```

Here's what's happening behind the scenes: Python creates a new directory called `myproject_env` that contains a copy of the Python interpreter, the standard library, and a `site-packages` directory where your project-specific packages will be installed.

You can name your virtual environment anything you like. Many developers use names like `venv`, `env`, or something descriptive like `django_project_env`. The name doesn't affect functionality, but choosing something meaningful helps you remember what each environment is for.

## Activating Your Virtual Environment

Creating the environment is just the first step. To actually use it, you need to activate it. The activation process varies slightly depending on your operating system:

**On Windows:**

```
myproject_env\Scripts\activate
```

**On macOS and Linux:**

bash

```bash
source myproject_env/bin/activate
```

When you activate a virtual environment, several important things happen. Your command prompt will change to show the environment name in parentheses, like `(myproject_env)`. This visual cue reminds you which environment is currently active. Additionally, the activation script modifies your PATH environment variable so that when you type `python` or `pip`, you're using the versions from your virtual environment rather than the system-wide versions.

## Working Within Your Virtual Environment

Once activated, you can install packages using pip, and they'll be installed only in this specific environment:

bash

```bash
pip install requests numpy django
```

To see what packages are currently installed in your active environment, use:

bash

```bash
pip list
```

This isolation is powerful because it means you can experiment with different package versions or install packages without worrying about breaking other projects or your system Python installation.

## Managing Dependencies

One of the most valuable practices when working with virtual environments is creating a requirements file. This file lists all the packages your project needs, making it easy for others (or future you) to recreate the exact same environment. Generate a requirements file with:

bash

```bash
pip freeze > requirements.txt
```

This command captures the current state of your environment, including specific version numbers. Someone else can then recreate your environment by creating their own virtual environment and running:

bash

```bash
pip install -r requirements.txt
```

## Deactivating and Removing Virtual Environments

When you're done working on your project, you can deactivate the virtual environment by simply typing:

bash

```bash
deactivate
```

Your prompt will return to normal, and you'll be back to using your system Python installation.

If you no longer need a virtual environment, you can delete it by simply removing the directory. Since virtual environments are self-contained, deleting the folder removes everything associated with that environment:

bash

```bash
rm -rf myproject_env  # On macOS/Linux
rmdir /s myproject_env  # On Windows
```

## Best Practices and Workflow Integration

Here's a workflow that many Python developers follow: Create a new directory for each project, immediately create and activate a virtual environment within that directory, then install only the packages that specific project needs. Keep your requirements.txt file updated as you add new dependencies.

Many developers also add their virtual environment directories to their `.gitignore` file because these environments can be easily recreated from the requirements.txt file, and including them in version control would add unnecessary bulk to your repository.

Consider this mental model: your virtual environment is like a clean laboratory bench for each experiment (project). You start with basic equipment (Python interpreter and standard library), then add only the specific tools (packages) you need for that particular experiment. When you're done, you can clean up completely without affecting your other laboratory setups.



