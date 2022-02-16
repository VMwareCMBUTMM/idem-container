# idem-container
Container image with Salt Idem built using PhotonOS 3.0.
* Python - python3-3.7.5-9.ph3
* Pip - python3-pip-3.7.5-9.ph3
* Idem - 15.0.0
* Idem_azure_auto - 0.0.3

## Download the latest image
The idem-container image is built and published to GitHub Container Repository (ghcr.io) and Docker Hub (docker.io).
```
# GitHub Container Repository
docker pull ghcr.io/vmwarecmbutmm/idem-container:latest
# Docker Hub
docker pull vmwarecmbu/idem-container:latest
```

## Usage

I have my project's credentials in `myproject/credentials.yaml` and I want to encrypt the file for use with idem.
```
~/SCRIPTS/idem-container ❯ tree
.
├── Dockerfile
└── myproject
    └── credentials.yaml <-- my credentials

1 directory, 2 files
```

I run the container image, mapping the project folder as a volume in the container, and execute the `encrypt` command on `myproject/credentials.yaml`:
```
~/SCRIPTS/idem-container ❯ docker run --rm -v $HOME/SCRIPTS/idem-container/myproject:/myproject vmwarecmbu/idem-container:latest encrypt myproject/credentials.yaml 
BJY9APhY-nA7PWrYhMU92gu7FAdQxT9sdlu21i-cX3o= 
```

The output of the `encrypt` command is the key for the encrypted credential file which has been created alongside the `credentials.yaml` file.
```
~/SCRIPTS/idem-container ❯ tree
.
├── Dockerfile
└── myproject
    ├── credentials.yaml
    └── credentials.yaml.fernet <-- encrypted credentials

1 directory, 3 files
```

I can now use the key and file to execute commands against the infrastructure - in this case I am describing the Azure Virtual Machines and using it to create a state file:

```
~/SCRIPTS/idem-container ❯ docker run --rm -v $HOME/SCRIPTS/idem-container/myproject:/myproject vmwarecmbu/idem-container:latest  describe azure.compute.virtual_machines --acct-key EnNNHzm42uoYif9oxKYh6k0eHlG42AitQmO7hb8iFkQ= --acct-file myproject/credentials.yaml.fernet > myproject/azure_vms.sls
```

Finally, the state (sls) file is created in my project folder:
```
~/SCRIPTS/idem-container ❯ tree 
.
├── Dockerfile
└── myproject
    ├── azure_vms.sls           <-- my state file
    ├── credentials.yaml
    └── credentials.yaml.fernet

1 directory, 4 files
```

## Create an alias
You can also create an alias for the docker command to run instead of typing the whole thing, for example:

```
alias idem="docker run --rm -v $HOME/SCRIPTS/idem-container/myproject:/myproject vmwarecmbu/idem-container:latest" 
```

and then use the alias to run my describe:
```
idem describe azure.compute.virtual_machines --acct-key EnNNHzm42uoYif9oxKYh6k0eHlG42AitQmO7hb8iFkQ= --acct-file myproject/credentials.yaml.fernet > myproject/azure_vms.sls
```