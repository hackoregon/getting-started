Setting up a Linux Laptop for Hack Oregon Projects
================
M. Edward (Ed) Borasky <znmeb@znmeb.net>

Bugs? Feature requests? Unclear documentation?
----------------------------------------------

File an issue at <https://github.com/hackoregon/linux-laptop-setup/issues/new>.

Design goals
------------

1.  Run at native speed on bare metal, avoiding the overhead of running as a guest in a virtual machine or container.
2.  Work on smaller / older machines that are too small to host a Docker container, a Vagrant box or a full virtual machine guest. This includes 32-bit machines to the extent that the applications allow.
3.  Provide Docker, Vagrant / VirtualBox and Virtual Machine Manager hosting on systems with the hardware capability.
4.  Modularity - you only install what you need to get your tasks done.
5.  Use Hack Oregon standard software versions whenever possible.

Which version of Linux should you use?
--------------------------------------

I've designed these scripts on Linux Mint 18 "Sarah" with the Cinnamon desktop (<https://www.linuxmint.com/download.php>). I test them on a Sarah 64-bit Cinnamon system, a Sarah 32-bit KDE virtual machine and an Ubuntu 16.04.1 32-bit virtual machine with the default "Unity" desktop.

Except for virtual machine hosting, they should work on any of the Linux Mint "Sarah" desktops and any Ubuntu 16.04.1 LTS "Xenial Xerus" desktop, 32 or 64 bits. File an issue at <https://github.com/hackoregon/linux-laptop-setup/issues/new> if you find problems on an Ubuntu 16.04.1 or Linux Mint 18 system.

Why Linux Mint 18 / Ubuntu 16.04.1 LTS?
---------------------------------------

1.  Long-term support: five years, starting in 2016!
2.  Safety in numbers: Linux Mint is the most popular community desktop distro, and Ubuntu LTS is the most popular community server distro.
3.  Third-party support: the first distros third parties test on are Ubuntu LTS and RHEL / CentOS. If they have oodles of resources, the next priority is the commercial SUSE Linux Enterprise. The rest of the distros - Fedora, Debian, openSUSE, Arch, Gentoo, etc. - are all fine distros, but unless you have an institutional reason to use them, you're essentially doing unpaid QA for a vendor / community.

A bit about hardware
--------------------

There are three kinds of Intel / AMD based PCs in use today:

1.  32-bit only: these are usually older machines, although some Atom-based machines sold today will only run 32-bit software. These machines will run the Python data science / machine learning tools, the database and GIS tools, the R platform and RStudio. They will not run Docker, Vagrant / VirtualBox or Virtual Machine Manager.
2.  64-bit without virtualization assists: these machines will run either 32-bit or 64-bit software. They will run everything a 32-bit machine will run and they will run Docker. They will not run Vagrant / VirtualBox or Virtual Machine Manager.
3.  64-bit plus virtualization assists (64-bit VA): these are the most recent machines. The virtualization assists are sometimes disabled in the BIOS / firmware. You will need to enable the assists if they are disabled. These machines will run everything in this collection.

Getting started
---------------

You'll need wall power and a reliable internet connection. Coffee shop WiFi can be problematic. I don't have an accurate RAM requirement defined yet but I know the `r-platform` script crashes if you don't have at least 1536 MiB. See <https://github.com/hackoregon/linux-laptop-setup/issues/11> and <https://github.com/tidyverse/readr/issues/544> for the gory details.

For virtual machine hosting, you need to have twice as much RAM in the host as the total RAM in all the active guests. My goal is for everything except virtual machine hosting to run in a 1 GiB machine.

1.  Download and unpack <https://github.com/hackoregon/linux-laptop-setup/archive/master.zip>. This will create a directory `linux-laptop-setup-master`.
2.  Open a terminal and `cd` into the directory.
3.  About `sudo`: `sudo` (super-user do) is a Linux utility that allows you to perform adminstrative tasks like installing software by temporarily operating as the `root` super-user. If you see the prompt `[sudo] password for <your username>:`, enter *your* password.

    `sudo` will remember that you have authenticated and not bother you again for a system-dependent time period. If that timeout expires, you will have to authenticate again. For install scripts that run a long time, you'll need to watch for this.
4.  If your machine doesn't have `git` and `vim`, type `./0gitvim`. Most Ubuntu systems I've looked at have them both, but my Linux Mint 18 system did not.
5.  If you haven't done a software upgrade in a while, do it now with `./0update-upgrade`. You will need to reboot after it completes.
6.  Type `./1core`. This will install a few core utilities.
7.  The scripts are modular - you only need to install what you're going to use.

Python data science / machine learning
--------------------------------------

We are using the Miniconda3 Python distribution for data science / machine learning tasks. To install the data science tools, type `./data-science-services`. You will get detailed install instructions when you run the script.

The data science services environment includes

-   Python 3
-   the Jupyter notebook server
-   IPython
-   NumPy
-   SciPy
-   Matplotlib
-   SymPy
-   pandas
-   scikit-learn (<http://scikit-learn.org/stable/>)
-   seaborn (<http://seaborn.pydata.org/>)
-   statsmodels (<http://statsmodels.sourceforge.net/>)

To start a Jupyter notebook with all the tools, open a terminal and enter `activate-data-science`. Your prompt will change to something like

    (data-science) <stuff>

and you can do all the command line operations. But you'll probably want to start a Jupyter notebook. To do that, type `jupyter notebook`. The notebook server will start up and a browser window / tab will open browsing to the server.

When you're finished, close the browser. Then type `Control-C` in the terminal window. You'll see

    Serving notebooks from local directory: /path/to/your/directory/
    0 active kernels 
    The Jupyter Notebook is running at: http://localhost:8888/
    Shutdown this notebook server (y/[n])?

Enter "y" to shut down Jupyter. Then enter `deactivate-data-science` and close the terminal.

### Optional: Exporting Jupyter notebooks as PDF files

Jupyter notebooks have the ability to export a copy of the notebook as a PDF file. However, this requires addtional packages from the TeXLive LaTeX distribution. These take quite a while to download and install, and will take up about 1.3 gigabytes on your hard drive. If you want this capabilily, type `./data-science-pdf`.

Database / SQL / GIS
--------------------

To install the PostgreSQL database and PostGIS GIS extensions, type `./database-gis-services`. This will install PostgreSQL 9.5 and PostGIS 2.2 and create a PostgreSQL super-user with the same user ID as your Linux user ID.

Note that as installed, the PostgreSQL service is only accessible inside the workstation / laptop. If you need to expose it to a local area network, you'll need to do some configuration.

QGIS and PgAdmin3 GUI tools
---------------------------

Type `.database-gis-gui` to install the QGIS (Quantum GIS) and PgAdmin3 desktop GUI tools.

Advanced tools
--------------

### The R platform

Type `./r-platform` to install the R platform. This includes:

-   R
-   Java
-   The `tidyverse` data wrangling, modeling and visualization packages
-   The `Shiny` interactive application development package
-   The `devtools` and `roxygen2` package development tools.

This takes a long time to install. You will probably have to watch it, because if the Linux package install takes long enough, it will pause wanting a `sudo` password entry.

#### Optional: R / RStudio authoring tools

The most recent release of RStudio includes tools for authoring websites and eBooks. Like the PDF export of Jupyter notebooks, these tools require a collection of packages from the TeXLive LaTeX distribution.

The R packages in this collection are `flexdashboard`, `bookdown`, `tufte` and `rticles`. They can be used from the command line, but they're much better with RStudio.

If you want the R authoring packages, type `./r-authoring`.

### RStudio Desktop

Type `./rstudio-desktop` to install the RStudio Desktop. You will need to install RStudio Desktop manually from the RStudio download website (<https://www.rstudio.com/products/rstudio/download3/>). The script will give you detailed instructions.

### RStudio Server

Type `./rstudio-server` to install the RStudio Server. The script will give you detailed instructions. You don't need both the desktop and the server, but it won't hurt anything to have both.

### Virtual Machine Manager (64-bit VA bare metal only)

The native Linux virtual machine hosting software is called Virtual Machine Manager. To install it, type `./virt-manager-hosting`. You will need to log out and back in again to join the `libvirtd` group. You will have a menu item added to start it.

### VirtualBox and Vagrant hosting (64-bit VA bare metal only)

If you want to host or build VirtualBox guests or Vagrant boxes, type `./vbox-vagrant-hosting`. The script will install VirtualBox automatically. Note that you do *not* need the non-open-source VirtualBox Extension pack.

You will need to install Vagrant manually from the HashiCorp Vagrant download website (<https://www.vagrantup.com/downloads.html>). The script will give you detailed instructions. You will need to log out and back in again after the install to join the `vboxusers` group.

### Docker hosting (64-bit or 64-bit VA, bare metal or inside a 64-bit guest machine)

If you want to run (or build) Docker images, install Docker hosting with `./docker-hosting`. You will need to log out and back in again to join the `docker` group.

### Git Large File Storage

We used this last year for Crop Compass. Note that GitHub charges money for both storage and download bandwidth for this, so be careful! If you need it, type `./git-lfs`.

Bugs? Feature requests? Unclear documentation?
----------------------------------------------

File an issue at <https://github.com/hackoregon/linux-laptop-setup/issues/new>.

Todo
----

1.  Instructions for connecting QGIS to the PostGIS database.
2.  Front-end tools: I'm not a front-end person so I have no idea what we'll need there. If there's something you want, file an issue and I'll add it.
3.  Django: The last I heard we'll be using Django for some projects. I am working through <https://www.amazon.com/Mastering-Django-Core-Complete-Guide-ebook/dp/B01KR6F4Z2> and can easily add install scripts if anyone wants them.
