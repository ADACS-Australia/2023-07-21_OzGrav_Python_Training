---
title: "Python project"
teaching: 15
exercises: 15
questions:
-
objectives:
-
keypoints:
-
---
## Parts of a python package

The two main parts of a python package are the executable scripts and the python modules.

The executable scripts are things that you run on the command line are installed by appending them to the`$PATH` environment variable so they can be run anywhere.
Executable scripts are usually short (only tens of lines of code) and call functions from the python modules.
They are most useful for automating tasks that you do regularly.

The python modules are python functions and classes that can be imported into other python scripts by appending them to the `$PYTHONPATH` environment variable.
Python modules are usually longer (hundreds of lines of code) and are used to implement the core functionality of your package.
They are useful to make new python scripts as they can be imported into other scripts.
Being able to only use parts of your code makes it more flexible and more useful for others.

## Creating a simple python package

To ensure that we understand the basics of the python package structure we will create a simple package the prints "Hello World!" to the screen.

First we shall make a function that prints "Hello World!" to the screen.
We will put this in a directory called `my_package` in a file called `my_file`.
We will also need to create a file called `__init__.py` in the `my_package` directory to tell python that this is a python module.


```
mkdir my_package
cd my_package
touch my_file.py
touch __init__.py
```

Next we will add the following code to `my_file.py`:

```
def hello_world():
    print("Hello World!")
```
{: .language-python}

This is our python module set up, we will now create an executable script that calls this function.

We will create a file called `hello_world.py` in the `my_package/scripts` directory.

```
mkdir scripts
cd scripts
touch hello_world.py
touch __init__.py
```

Next we will add the following code to `hello_world.py`:

```
from my_package.my_file import hello_world

def main():
    hello_world()

if __name__ == "__main__":
    main()
```
{: .language-python}

This is our executable script set up, we will now make a `setup.py` it so that we can run it from anywhere.

```
cd ../../
touch setup.py
```

Next we will add the following code to `setup.py`:

```
'''
Setup for my_package that describes it's contents and how to install it.
'''
from setuptools import setup

# List of dependencies for the package
# >= Can be used to specify a minimum version
# >=,< Can be used to specify a minimum and maximum version e.g. 'numpy>=1.15,<1.20',
dependencies = [
    'argparse>=1.4.0',
    'numpy>=1.15',
    'matplotlib>=2.1.0',
    'astropy>=2.0.2',
]

setup(
    # Name of the repository
    name='my_project',
    # Version of the software (keep this up to date with git tags/releases)
    version=1.0,
    # Short description of the package
    description='An example package to be used as a template',
    # Update this with your github URL
    url='https://github.com/ADACS-Australia/python_project_template',
    # Minimum version of python required
    python_requires='>=3.6',
    # Name of your package, should be the same as the name of the directory
    packages=['my_package'],
    # Any data files that need to be included with the package (non python files)
    package_data={'my_package':['data/*.csv']},
    # Dependencies for the package
    install_requires=dependencies,
    # Scripts that will be run on the command line
    entry_points={
        'console_scripts': [
            # Make a hello_world command that runs the main function in hello_world.py script located in my_package/scripts
            'hello_world=my_package.scripts.hello_world:main',
        ],
    },
)
```
{: .language-python}

This actually includes a bit more than we need for our simple script but we will use it as a template for future packages.
To confirm all the files are in the correct place we can run:

```
tree
```

Which should output:

```
.
├── my_package
│   ├── __init__.py
│   ├── my_file.py
│   └── scripts
│       ├── __init__.py
│       └── hello_world.py
└── setup.py
```

We can now install our package by running:

```
pip install .
```

We can now run (from any directory) our script by running:

```
hello_world
```

Congratulations you have made your first python package!


## Turning out initial script into a python package

Now that we have understand the parts of a python package we will turn our initial script into a python package.

