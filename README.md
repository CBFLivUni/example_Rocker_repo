# how to set up a repo with authentication using SSH keys

1) Create a repository for your project (like this one)
2) Start a new "rocker" Docker container for whichever version of R you want. If you just want the latest rocker build you can specify "latest" instead of a version number. Example command to start the Docker container is below.
3) You will then have a blank RStudio session where the only thing visible is the directories you linked to the Container
4) You should link the directory on your local file system one level above where you want to clone your github repository. This is because within the docker container you can't clone a github repo to ~/ (your home directory)
5) Git will already be installed as part of the container, but the software to generate the SSH keys is not, so run the following in a bash terminal

sudo apt-get update
sudo apt install openssh-client

6) Now we need to set up our container to use our github username and email address. Run the following in a bash terminal:

git config --global user.email "you@example.com"
git config --global user.name "Your Name"


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

13) On the github website go to your github settings, click SSH and GPG keys, click "New SSH key", give the key a meaningful name and paste it into the box, then click "Add SSH key". You should see something that looks like this:

![image](https://user-images.githubusercontent.com/35961519/110942196-62411280-8331-11eb-8e0c-2fb64a81efc5.png)

14) Now that we have the private key saved in our container and the public key on Github we are ready to make a new RStudio project, link it to the github repository and authenticate using the keys.

15) First we need to get the SSH link for the repository on github
16) Navigate to the repository you want to clone on the github site, for example https://github.com/CBFLivUni/example_repo
17) Click "Code", then underneath where it says "Clone" click the "SSH" button. Copy the address to your clipboard, it will look like: git@github.com:CBFLivUni/example_repo.git

18) In RStudio go to File > New project.
19) Click Version control
20) Click Git
21) Enter the address of the repository from your clipboard where it says "Repository URL". The Project directory name will be automatically populated.
22) Choose where on your container you want to clone the repository. You can choose ~, in which case it will be cloned to /home/rstudio/your_repo_name.
23) Click Create Project.
24) It may ask whether you trust the authenticity of host "github.com", type "yes". If you want you can verify that the RSA key in the warning messsage matches that on the github website (https://docs.github.com/en/github/authenticating-to-github/githubs-ssh-key-fingerprints)

16) If all is successful you will have a new RStudio project and you should be able to see all the files present in the github repository (if it is new it will probably just be a few: .gitignore, README.md and an Rproj file.
