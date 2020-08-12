# Using Conda environment in Jupyter


## Problem

I have a base conda environment that has too many packages that causes conda installation to be too slow. Conda environments were designed to be small and contain only packages that belong to the "project".

## Solution

We do this by creating a kernelspec in the target jupyter environment that points to a kernel that is in the source conda environment.

## TLDR; Steps

```
# Create a python 3.7 environment with scikit-learn
conda create -n source_env python=3.7 scikit-learn
```

```
# Activate the conda environment which you want to use in jupyter
conda activate source_env
```

```
# Install ipykernel into the "source" conda environment

conda install -y ipykernel
```


```
# Now install this kernel into the "target" jupyter 
using the following command:


python -m ipykernel install --prefix=/opt/conda --name 'scikit-learn-env' [1]

Translation: Install ipykernel in "source_env" environment to the jupyter that is prefix by "/opt/conda" environment with the name "scikit-learn-env"

The above command will create a kernelspec in jupyter environment for this kernel 
```

Now if you open your Jupyter Lab and check the Kernels, you will see scikit-learn-env as one of the kernels.

On selection, now your jupyter is using the kernel in the "source" environment to run the code cells that we send.

Yay! Are we done?

Not yet. There is a problem.

If you run
```
!echo $PATH
```
path does not get activated with our "source" environment.

This implies that any binary that we download as a part of "source" environment will not be discoverable.

### Resolution

We need to be update the kernelspec to include activating conda-environment.

When we ran command [1], the command created a kernelspec as mentioned above. This kernelspec contains a kernel.json that tells jupyter what command to run when the kernel starts, we need this command to include the conda activation. Replace the contents of kernel.json with this below JSON.


```
{
  "argv": [
    "bash",
    "-c",
    "source /opt/conda/bin/activate /opt/conda/envs/source_env && exec /opt/conda/envs/source_env/bin/python -m ipykernel_launcher -f '{connection_file}'"
  ],
  "display_name": "Scikit-Learn Env",
  "language": "python"
}
```


There you have it. Now if you go into the jupyter notebook.

```
!echo $PATH
```

This should have /opt/conda/envs/source_env prepended to it.

# References

 - [Kernel Installation](https://ipython.readthedocs.io/en/stable/install/kernel_install.html)
 - [Conda Activation](https://github.com/ipython/ipykernel/issues/416)

 
