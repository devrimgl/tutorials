-*- mode: markdown;mode:visual-line;  fill-column: 80 -*-

Authors: Clément Parisot

Copyright (c) 2018 UL HPC Team  -- see <http://hpc.uni.lu>

---------------------------------------------------------
# UL HPC Tutorial: [Advanced] Python : Use Jupyter notebook on UL HPC

[![By ULHPC](https://img.shields.io/badge/by-ULHPC-blue.svg)](https://hpc.uni.lu) [![Licence](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](http://www.gnu.org/licenses/gpl-3.0.html) [![GitHub issues](https://img.shields.io/github/issues/ULHPC/tutorials.svg)](https://github.com/ULHPC/tutorials/issues/) [![](https://img.shields.io/badge/slides-PDF-red.svg)](https://github.com/ULHPC/tutorials/raw/devel/advanced/Python2/tutorial_python.pdf) [![Github](https://img.shields.io/badge/sources-github-green.svg)](https://github.com/ULHPC/tutorials/tree/devel/advanced/Python2/) [![Documentation Status](http://readthedocs.org/projects/ulhpc-tutorials/badge/?version=latest)](http://ulhpc-tutorials.readthedocs.io/en/latest/advanced/Python2/) [![GitHub forks](https://img.shields.io/github/stars/ULHPC/tutorials.svg?style=social&label=Star)](https://github.com/ULHPC/tutorials)

[![](https://github.com/ULHPC/tutorials/raw/devel/advanced/Python2/cover_slides.png)](https://github.com/ULHPC/tutorials/raw/devel/advanced/Python2/tutorial_python.pdf)

Python is a high-level interpreted language widely used in research. It lets you work quickly and comes with a lot of available packages which give more useful functionalities.

# Reserve a node with X11 redirection

Due to an issue on iris, you will need a specific script to reverse a node with X11 forwarding. You can clone this script [on jbornschein srunx11 repo on Github](https://github.com/jbornschein/srun.x11.git).

```
# Connect to iris using the X11 forwarding
ssh -X iris-cluster
git clone https://github.com/jbornschein/srun.x11.git
# Reserve an node interactively
./srun.x11/srun.x11
```

# Load Python and install required modules in a virtualenv

If you have never used virtualenv before, please have a look at [Python1 tutorial](http://ulhpc-tutorials.readthedocs.io/en/latest/advanced/Python1/). 

```
# Load your prefered version of Python
module load lang/Python/3.6.0-foss-2017a-bare
# Create a new virtualenv
cd tutorials/advanced/Python2
pip install --user virtualenv
virtualenv venv
source venv/bin/activate
```

Now we need to install all the modules needed. They are listed in the requirements.txt at the root of our tutorial directory. Here is the list of essentials ones:

```
# jupyter himself
pip install jupyter
# matplotlib to plot the graph inside your notebook
pip install matplotlib
# ipyparallel for parallel execution of your code on several thread and/or nodes
pip install ipyparallel
# mpi4py for mpi integration in python
pip install mpi4py
# To use our virtualenv in the notebook, we need to install this module
pip install ipykernel
```

To install everything:

```
pip install -r requirements.txt
```

Now everything is installed properly.

# Create your own kernel and launch your Notebook

In order to access to all the modules we have installed inside your Notebook, we will need to create a new Kernel and use it inside Jupyter.

To do so, let's use `ipykernel`.

```
python -m ipykernel install --user --name=venv
```

Now we will have to start our first notebook. To have access to it from the outside, we will need to run it on the correct IP of the node. This simple command permits to start a new notebook with the correct IP. Please ensure that you are running the command inside the correct directory!

The `--no-browser` command is used to disable the openning of the browser after the start of the notebook. We use `--generate-config` at first to generate a default configuration. The default configuration is stored in `~/.jupyter/jupyter_notebook_config.py`

To make things easier, we will protect our Notebook with a password. You have just to choose a password after typing the `jupyter notebook password` command. A hash of your password will be stored in the jupyter config file. 

```
cd tutorials/advanced/Python2
jupyter notebook --generate-config
jupyter notebook password
jupyter notebook --ip $(ip addr show em1 | grep 'inet ' | awk '{print $2}' | cut -d/ -f1) --no-browser
```

At the end of the command, you should see a link like this:

```
[I 17:45:05.756 NotebookApp] Serving notebooks from local directory: /mnt/irisgpfs/users/cparisot/Python2
[I 17:45:05.756 NotebookApp] 0 active kernels
[I 17:45:05.756 NotebookApp] The Jupyter Notebook is running at:
[I 17:45:05.757 NotebookApp] http://172.17.6.55:8888/
[I 17:45:05.757 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```

Now we need to create an SSH tunneling in order to access this URL on your laptop. We will forward every local requests made on your localhost address of your laptop the cluster. The simpliest way to do so is to type this command if your are on Linux:

```
ssh -NL 8888:<IP OF YOUR NODE>:8888 iris-cluster
# In my example, my command will be: ssh -NL 8888:172.17.6.55:8888 iris-cluster
```

Then, by accessing to your local port 8888 on the localhost address 127.0.0.1, you should see the Python notebook. 

[http://127.0.0.1:8888/](http://127.0.0.1:8888/).

If you haven't chosen a password to protect your notebook, please append the token to the URL before accessing your Notebook.

# Run our first notebook

* Just click on the Notebook **TODO add a notebook for simple stuff**.
* Change the kernel for the `venv` one
  * Go onto **Kernel** tab
  * Choose **Change kernel**
  * Select our previously generated kernel called **venv**
* Try to run the code of the notebook in the kernel by using 'Alt-Enter' keys.

# Let's parallelize everything !

## Create your computing cluster

Firstly enable the extension and then start a cluster locally:

```
ipcluster nbextension enable --user
ipcluster start --engines=MPI
```

Then run our dedicated notebook (by using the venv kernel) and that's it !

