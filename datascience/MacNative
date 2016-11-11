## Native OSX/macOS installation instructions






### PostgreSQL installation
Our base install version for Hack Oregon is 9.5.x.  Please keep this in mind when you are downloading whatever installation package you choose.  


There are [several ways](https://www.postgresql.org/download/macosx/) to install postgres on your mac.  You should do whatever works well for you but we have found that the Postgres.app works well.  It ships with PostGIS and will keep the installation separate from any other versions of Postgres you already have installed.  Homebrew can sometimes be tricky but the current base install is 9.5.5.  There are also version specific packages for homebrew.  There are also other options listed on the page linked above.


**Installation via Postgres.app**
* [Download](https://github.com/PostgresApp/PostgresApp/releases/download/9.5.5/Postgres-9.5.5.zip) the v9.5.5 installer and unzip it and move the .app to your Applications directory (just as you would any other app)
* When you run the app, the server will start and you’ll see an elephant icon appear in your menu bar.  


**Installation via homebrew**
    $ brew update && brew install postgres
If you have a different version of postgres already installed, you can do a version specific installation with
    $ brew update
    $ brew tap petere/postgresql
    $ brew install postgresql-9.5


Note sure if this will work across different versions but you can also install PostGIS through homebrew::
    $ brew install postgis




**Ports and Paths**
In theory, you can have many different versions of postgres running at the same time but there are two things you will need to do to manage this. 
1. Each postgres installation will need to be listening on a different port to ensure no port conflicts.  The postgresql.conf file can be set to listen on a specific port.  
2. Only one set of command line tools can be available at once.  The PATH variable can should be set to point to the bin directory of whichever version of postgres you want to use.  If you’re switching between two different homebrew versions, this can be done using the brew link command.






### Anaconda Installation and Environment Setup


[Anaconda](https://en.wikipedia.org/wiki/Anaconda_(Python_distribution)) is “a freemium open source distribution of the Python and R programming languages for large-scale data processing, predictive analytics, and scientific computing, that aims to simplify package management and deployment.”  It has the ability to install various collections of Python packages into separate environments, allowing you to minimize package requirement collisions and even quickly and easily switch from Python 2 to Python 3 if need be.  It is largely a command line interface.


**Installation via package download**


There are essentially two paths to installation:


* [Miniconda](http://conda.pydata.org/miniconda.html), where the base install is small but creating each environment takes a bit more work
* [Anaconda](https://www.continuum.io/downloads), with a large base that saves some time on creating environments.


Both should work for our purposes; if you think you might continue to work on Python beyond this project it might be worth springing for the full Anaconda setup.  Either way, download the Python 3 installer from the links above; it should be fairly straightforward to follow along.


**Installation via homebrew**


Installation via homebrew is *not recommended*.


**Using environments in Anaconda**


[Managing Environments (conda docs)](http://conda.pydata.org/docs/using/envs.html)


What Anaconda basically does is create environments in separate subfolders of its install, then uses scripts to manage environment variables keeping them pointed at the python libraries and executable you intend to use.


You first need to create an environment.  The following command in a terminal will create a fairly complete data science environment:


    conda create -n data-science jupyter scikit-learn seaborn statsmodel django


`conda create` obviously creates an environment.  The `-n` or `--name` flag declares the name of that environment.  If you wanted Python 2.7, at this point you would add `python=2.7`.  The remaining list of packages are what conda will attempt to install at creation; it is not aware of all Python packages, but knows of around 300 and their dependencies.  For example, seaborn is collection of wrappers for nice matplotlib plots, so the above command will instal matplotlib along the way.


To get into this environment, you just run `source activate data-science`.  `source` is a builtin bash command that runs a script; `activate` is a script Anaconda installed to enter an environment; `data-science` is the name of your environment, which `activate` takes as an argument.


Once you’re in the environment, it’s a simple matter to install additional packages.  `conda install <package>` will install any package conda knows, and will also attempt to resolve package requirements.  If conda doesn’t know of the package, it is still possible to use `pip` (installed in every conda environment) or to compile packages from source if need be.


To leave the environment, run `source deactivate`.


If at any point you’d like to see what environments you’ve set up, the following command will print a list:


    conda info --envs


It will also place an asterisk in the line of the environment you’re currently using.


Finally, to delete an environment:


    conda remove --name data-science --all


