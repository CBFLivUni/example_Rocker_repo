# example_repo
how to set up a repo with authentication using keys

[work in progress]

1) Create a repository for your project (like this one)
2) Start a new "rocker" Docker container for whichever version of R you want. If you just want the latest rocker build you can specify "latest" instead of a version number. Example command to start the Docker container is below.
3) You will then have a blank RStudio session where the only thing visible is the directories you linked to the Container
4) You should link the directory on your local file system one level above where you want to clone your github repository. This is because within the docker container you can't clone a github repo to ~/ (your home directory)
5) Git will already be installed as part of the container, but the software to generate the SSH keys is not, so run the following in a bash terminal

sudo apt-get update
sudo apt install openssh-client

6) Now we need to create a public/private SSH key pair that will enable us to authenticate with github without usernames/passwords. 
7) This next part is pretty much copied from the github documentation here: https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
8) Run the following in a terminal. Choose a password if you want (something you will remember, this will be a very easy one to forget)

ssh-keygen -t ed25519 -C "your_email@example.com"

9) You should have two files: ~/.ssh/id_ed25519 and ~/.ssh/id_ed25519.pub
10) Start the ssh-agent

eval "$(ssh-agent -s)"

11) Add the SSH private key to the ssh-agent. You should see "Identity added: /home/rstudio/.ssh/id_ed25519 (your_email@email.com)

ssh-add ~/.ssh/id_ed25519

12) Copy the contents of the public key to the clipboard. You can simply run "cat ~/.ssh/id_ed25519.pub" and copy the contents from the terminal window. 

It will look like this: 

ssh-ed25519 DABAC3NzaC1lZDI1NTE5AAAAIMd5fYkW8tqmXme7ge+/oXws14kLXTilQZSKo+wBq0sP your_email@email.com



Go to your github settings, click SSH and GPG keys

4) Make the new rstudio project
5) Link the repository to your rstudio
