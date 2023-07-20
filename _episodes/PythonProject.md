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


## The template we will be working toward

We have created a [template](https://github.com/ADACS-Australia/python_project_template) with the basic structure of a python package.
This can be used a starting point for your own python packages and has some of the documentations and tests set up so you can see how they work and edit them for your purposes.
We will slowly work towards a complete version of this template over the course of the workshop.

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
We will split the main two tasks of out initial script into two functions, one that reads in the data and one that plots it, then create an executable scripts that calls these functions.

First we will create a module file called `my_package/data_processing.py` that contains the following code:

```
import pandas as pd
from astropy.coordinates import SkyCoord
import astropy.units as u

def input_data(csv_path):
    # Read the CSV file into a DataFrame
    df = pd.read_csv(csv_path)

    # Add new columns to df
    df['RA (deg)']  = 0
    df['Dec (deg)'] = 0

    # Loop over dataframe and convert RA and Dec to degrees
    for index, row in df.iterrows():
        ra_hms  = row['RA (HMS)']
        dec_hms = row['Dec (DMS)']

        # Create a SkyCoord object and specify the units
        c = SkyCoord(ra=ra_hms, dec=dec_hms, unit=(u.hourangle, u.deg))
        # Access the converted RA and Dec in degrees
        ra_deg  = c.ra.deg
        dec_deg = c.dec.deg

        # Update the DataFrame with the converted values
        df.at[index, 'RA (deg)']  = ra_deg
        df.at[index, 'Dec (deg)'] = dec_deg

    return df
```
{: .language-python}

This was as easy as copying the code from our initial script into a function, declaring `csv_path` as an input and `df` as an output.
It is a good habit to use docstrings to describe your functions, but we will go over that later in the [documentation section]({{page.root}}{% link _episodes/Docs.md%}#docstrings).

Next we will create another module file called `my_package/plotting.py` that contains the plotting functions.
We create a new module file for plotting as it is a different type of task to reading in the data and it is good practice to separate different tasks into different modules to keep your package organised.

```
def molleweide_plot(df):
    # Plot the pulsars
    fig = plt.figure(figsize=(6, 4))
    # Molleweide projection gives us a nice view of the whole sky
    ax = plt.axes(projection='mollweide')
    plt.grid(True, color='gray', lw=0.5, linestyle='dotted')
    ax.set_xticklabels(['22h', '20h', '18h', '16h', '14h','12h','10h', '8h', '6h', '4h', '2h'])

    # Convert RA and Dec to radians and plot
    ax.scatter(radians(-df['RA (deg)'] + 180), radians(df['Dec (deg)']), s=0.2)
    plt.savefig("pulsar_plot.png", dpi=300, bbox_inches='tight')
```
{: .language-python}

We can now create a new script called `my_package/scripts/filter_and_plot.py` that will call these functions:

```
from my_package.data_processing import input_data


def main():
    print("Reading in the data")
    df = input_data("pulsars.csv")

    print("Plotting the data")
    molleweide_plot(df)


if __name__ == "__main__":
    main()
```
{: .language-python}

Finally, to be able to install this as an executable script we must add the following to the `setup.py` file:

```
setup(
    #...
    entry_points={
        'console_scripts': [
            'hello_world=my_package.scripts.hello_world:main',
            # Above is test script and below is new script that must be added
            'filter_and_plot=my_package.scripts.filter_and_plot:main',
        ],
    },
    #...
)
```
{: .language-python}

We can now install our package by running:

```
pip install .
```
{: .language-bash}

And run our script by running:

```
filter_and_plot
```
{: .language-bash}

Huzzah! We now have an installable script that we can run from any directory and that we can share with others.
BUT, we are not quite done yet.

## Using an argument parser

We can improve our script by adding an argument parser.
This will allow us to specify the path to the CSV file as an argument when we run the script.
We will use the [argparse](https://docs.python.org/3/library/argparse.html) module to do this.

First we will add the following to the `my_package/scripts/filter_and_plot.py` script:

```
import argparse

from my_package.data_processing import input_data, filter_by_name, filter_by_declination
from my_package.plotting import molleweide_plot


def main():
    # Create an argument parser so users can understand and use command line options
    parser = argparse.ArgumentParser(description='Process and filter an input CSV file and create a sky plot of the sources.')

    # Add arguments to the parser
    parser.add_argument(
        # A short version of the argument which is normally a single dash and a single letter
        '-i',
        # A long version of the argument which is normally two dashes and a word
        '--input',
        # The type of the argument that will be converted when it is read in
        type=str,
        # A description of what the argument does
        help='The path to the input csv file. Default: "pulsars.csv"',
        # The default value of the argument if none is provided
        default="pulsars.csv",
    )

    # Parse the arguments
    args = parser.parse_args()

    # Read in the data
    df = input_data(args.input)

    print("Plotting the data")
    molleweide_plot(df)

if __name__ == "__main__":
    main()
```
{: .language-python}

Now we can run our script with the `-h` or `--help` flag to see the help message:

```
filter_and_plot -h
```
{: .language-bash}


```
usage: filter_and_plot [-h] [-i INPUT]

Process and filter an input CSV file and create a sky plot of the sources.

options:
  -h, --help            show this help message and exit
  -i INPUT, --input INPUT
                        The path to the input csv file. Default: "pulsars.csv"
```
{: .output}

So if you ever forgot what the script does or what it's options are, you can now rely on the help message to remind you.
As we add more features, we can add more arguments to the parser to make our script more flexible.

So for example if the CSV file is in a different directory we can now run:

```
filter_and_plot -i /path/to/my/csv/file.csv
```
{: .language-bash}

## Adding data files

That's all pretty cool but what if we want to add some data files to our package so we don't have to download them and point to them with environment variables?
We can do this by adding a `package_data` argument to the `setup.py` file:

```
setup(
    #...
    package_data={
        'my_package': ['data/*.csv'],
    },
    #...
)
```
{: .language-python}

This will include all csv files in the `my_package/data` directory in the package when we run `pip install .`.
Remember to make this directory and move the `pulsars.csv` file into it.
One easy what to then access the files is to create a a module called `my_package/load_data.py` that contains the following:

```
import os

# Path to the pulsar CSV data file relative to the current file location
PULSAR_CSV_PATH = os.path.join(os.path.dirname(__file__), 'data/pulsars.csv')
```
{: .language-python}

We can then import this module and use the `PULSAR_CSV_PATH` variable to access the data file.
The standard practice is to name this variable in capitals to indicate that it is a constant used outside of function scopes (won't change and is not used as an input to a function).
So for example we can set it as the default value for the input argument in the `my_package/scripts/filter_and_plot.py` script:

```
import argparse

from my_package.data_processing import input_data, filter_by_name, filter_by_declination
from my_package.plotting import molleweide_plot
from my_package.load_data import PULSAR_CSV_PATH


def main():
    # Create an argument parser so users can understand and use command line options
    parser = argparse.ArgumentParser(description='Process and filter an input CSV file and create a sky plot of the sources.')

    # Add arguments to the parser
    parser.add_argument(
        # A short version of the argument which is normally a single dash and a single letter
        '-i',
        # A long version of the argument which is normally two dashes and a word
        '--input',
        # The type of the argument that will be converted when it is read in
        type=str,
        # A description of what the argument does
        help='The path to the input csv file. If none is provided the default pulsar data will be used.',
        # The default value of the argument if none is provided
        default=PULSAR_CSV_PATH,
    )
    #...

```
{: .language-python}

So now we can run the script without any arguments and it will use the default data file
