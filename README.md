# how to set up an RStudio server docker container and link it to a repository with authentication using SSH keys

### This assumes you have docker installed on a Linux machine and the permission to run it.

### Remember that github cannot store large amount of data, so keep your data separate or add your data directory to your .gitignore file.

1) On github, create a repository for your project (like this one)
2) On your linux machine, start a new "rocker" Docker container for whichever version of R you want. If you just want the latest rocker build you can specify "latest" instead of a version number. An example command to start the Docker container is below. The -v (volume) option links a directory on the local filesystem to a location within the container. The locations after the ":" will be created for you in the container. This does not copy the files, it enables the docker container to access the files on the local filesystem, so any changes you make are visible from outside the docker container. If you delete the files from within the docker container, they are gone forever. If you delete the docker container, your files will not be deleted. The github repository will always be cloned into a new directory in your container. I don't think it's possible to have your home directory in the container (/home/rstudio) also be the cloned github repository. 

```
sudo docker run -d --cpus 30 -p 8796:8787 -e DISABLE_AUTH=true -e ROOT=TRUE -e PASSWORD=rocker --name example_rocker_with_github \
        -e USERID=$(id -u) \
        -v /home/spectres/ms_single_cell/blood_and_csf_data:/home/rstudio/ms_project \
        -v /home/spectres/cbf-fs/active_projects:/home/rstudio/ms_data \
        rocker/tidyverse:4.0.3
```


3) You will then have a blank RStudio session accessible via your browser using the port you specified (if you are running it on your own computer, localhost:8787 is the default address). Change the first part of the -p option in the docker run command to change the port you use (for example: 8000:8787 will make the server accessible on port 8000). The only files visible should the directories you linked to the container using -v.

4) Git will already be installed as part of the container, but the software to generate the SSH keys is not, so run the following in a bash terminal. RStudio-server has a terminal window built in, just click the "Terminal" tab at the top left of the console.

```
sudo apt-get update
sudo apt install openssh-client
```
5) Now we need to set up our container to use our github username and email address. Run the following in a bash terminal:
```
git config --global user.email "you@example.com"
git config --global user.name "username"
```

6) Now we need to create a public/private SSH key pair that will enable us to authenticate with github without usernames/passwords. 
7) This next part is pretty much copied from the github documentation here: https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
8) Run the following in a terminal to generate a new SSH key. Choose a password if you want (something you will remember, this will be a very easy one to forget)
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```
9) You should now have two files: ~/.ssh/id_ed25519 and ~/.ssh/id_ed25519.pub
10) Start the ssh-agent so we can add the key to our system, you should usee the process ID of the agent printed in the terminal.
```
eval "$(ssh-agent -s)"
```
11) Add the SSH private key to the ssh-agent. You should see "Identity added: /home/rstudio/.ssh/id_ed25519 (your_email@email.com)
```
ssh-add ~/.ssh/id_ed25519
```
12) Copy the contents of the public key to the clipboard. You can simply run "cat ~/.ssh/id_ed25519.pub" and copy the contents from the terminal window. 

It will look like this but will be unique to your project: 
```
ssh-ed25519 DABAC3NzaC1lZDI1NTE5AAAAIMd5fYkW8tqmXme7ge+/oXws14kLXTilQZSKo+wBq0sP your_email@email.com
```
13) On the github website go to your github settings, click SSH and GPG keys, click "New SSH key", give the key a meaningful name and paste it into the box, then click "Add SSH key". You should see something that looks like this:

![image](https://user-images.githubusercontent.com/35961519/110942196-62411280-8331-11eb-8e0c-2fb64a81efc5.png)

14) Now that we have the private key saved in our container and the public key on Github we are ready to make a new RStudio project, link it to the github repository and authenticate using the keys.

15) First we need to get the SSH link for our repository on github
16) Navigate to the repository you want to clone on the github site, for example https://github.com/CBFLivUni/example_repo
17) Click "Code", then underneath where it says "Clone" click the "SSH" button. Copy the address to your clipboard, it will look like: git@github.com:CBFLivUni/example_repo.git
18) In RStudio go to File > New project > Version control > Git.
19) Paste the address of the repository from your clipboard where it says "Repository URL". The Project directory name will be automatically populated. RStudio will automatically detect that this is an SSH address.
20) Choose where on your container you want to clone the repository. You can choose ~, in which case it will be cloned to /home/rstudio/your_repo_name.
21) Click Create Project.
22) It may ask whether you trust the authenticity of host "github.com", type "yes". If you want you can verify that the RSA key in the warning messsage matches that on the github website (https://docs.github.com/en/github/authenticating-to-github/githubs-ssh-key-fingerprints)

16) If all is successful you will have a new RStudio project and you should be able to see all the files present in the github repository (if it is new it will probably just be a few: .gitignore, README.md and an Rproj file.
17) Try creating a few files and committing and pushing them, they should appear instantly on the github repository.
