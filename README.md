nc_rstudio is an OpenOndemand app that runs RStudio.

## Setup

To use this OOD app, you need to install the following on every node, e.g. to a shared directory such as NFS or Lustre, or if you don't have either just rsync the generated directories consistently between all nodes.

* R Installation
* Lmod module `rserver` - This lmod module will then be used to set the command `rserver` in the PATH. You can create different .lua files can be labeled with different R + rserver versions so some users can downgrade as needed.
<!--
### Create conda environment for jupyterlab

As the owner of the lmod module directory (such as `hpc_test`), run the following commands:

```
$ module load conda
$ conda create -p /data/apps/miniforge/25.11.0/envs/jupyterlab jupyter pandas
```

Now create an an Lmod module for this environment at `/etc/modulefiles/jupyterlab/py312-25.11.0.lua`, named after the python version used and conda version since JupyterLab changed some config settings in Python 3.12 we need to be able to disambiguate.

```
local modroot = "/data/apps/miniforge/25.11.0/envs/jupyterlab"               
local pyver   = "3.12.12"                                                       
                                                                                
pushenv("CONDA_PREFIX",modroot)                                                 
pushenv("CONDA_DEFAULT_ENV",modroot)                                            
prepend_path("LD_LIBRARY_PATH",modroot.."/lib")                                 
prepend_path("PYTHONPATH",modroot.."/lib/python3.12/site-packages")             
pushenv("PYTHONNOUSERSITE","1")                                                 
                                                                                
-- Using Python and tools from the environment
prepend_path("PATH",modroot.."/bin")
```

Now this should be usable with the OOD app.
-->


### Posit Maintained RStudio Server

RStudio Server is an IDE for R that can be run as a webui.

Unlike most other RPMs, the Posit RStudio RPMs can be unpacked to a shared directory so that multiple different versions can exist at once. Otherwise, the RStudio version will constantly change. Follow these steps to install two versions of RStudio at the same time.

https://posit.co/download/rstudio-server/

Create a /data/apps/rstudio/server directory with the specific version number, and unpack the RPM into it.

```
mkdir -p /data/apps/rstudio/server/2025.09.2/
cd /data/apps/rstudio/server/2025.09.2

curl -O https://download2.rstudio.org/server/rhel9/x86_64/rstudio-server-rhel-2025.09.2-418-$(arch).rpm

rpm2cpio rstudio-server-rhel-2025.09.2-418-$(arch).rpm | cpio -idm
```

The rstudio server binary is available at `/data/apps/rstudio/server/2025.09/usr/lib/rstudio-server/rstudio-server` .

https://support.posit.co/hc/en-us/articles/1500008850802-Using-Posit-Workbench-with-SELinux


## Deployment

Place this git repo into `/var/www/ood/apps/usr` . Make sure permissions on it are set to chmod 755, or it will not appear.

```
cd /var/www/ood/apps/usr/
sudo git clone https://github.com/PATH/nc_common.git
sudo git clone https://github.com/PATH/TO-YOUR-REPO.git
sudo chown root:root -R /var/www/ood/apps/usr/
```

Ensure that `nc_common` also exists in the same directory, since it imports form and attributes from it.

Make sure that `chmod +x /var/www/ood/apps/usr/nc_rstudio/templates/*` is set. It would already be set if you git cloned, but there might be issues.

You can set chmod 700 on a directory to hide the app from OOD.
