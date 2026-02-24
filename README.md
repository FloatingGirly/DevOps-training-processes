# Student Guide: Git + Podman + GitHub Actions (Windows)

## What you will do
1) Pull code from GitHub (Git clone)  
2) Run the app locally (Python)  
3) Run tests (pytest)  
4) Build + run the app as a container (Podman)  
5) Make a change on a branch, push, open a Pull Request, and watch CI run

---

## Prerequisites (install once)
- Git for Windows
- Python 3.12 (or 3.11+) (python.org/downloads/windows -> Windows installer (64-bit) -> Ensure "add to PATH" is ticked on initial install screen)
- VS Code (recommended), Powershell or Git Bash
- Podman Desktop (recommended) OR Podman CLI set up on Windows
  - Podman Desktop usually uses a VM/WSL2 behind the scenes
- WSL for Windows

> If you have installed any tooling please ensure that you have restarted your system before proceeding with any of the steps below

---

## Step 1: Clone the repo
1) Open Git bash and navigate to your desktop
```
cd ~/desktop
```
2) Clone down the Github code
```
git clone https://github.com/AndPDale/DevOps-training-processes.git
```

2.1) Change directory into the Github code
```
cd DevOps-training-processes
```

3) Install dependencies:
```
pip install -r requirements.txt
```

4) Run the app:
```
python ./app.py
```

4.1) Open your application in your browser by visiting:

[http://127.0.0.1:5000](http://127.0.0.1:5000)

4.2) Try out the application: Enter name + points + multiplier and click Calculate score

(Optional) Open the APIs to check health:
http://127.0.0.1:5000/api/health
http://127.0.0.1:5000/api/score?points=10&multiplier=2

5) Return to Git Bash to stop the app with:
```
Ctrl + C
```
Your terminal will return to the command input

<code style="color : Red">🚀  Well Done you just run a Python application</code>

## Step 2: Setup Podman
6) Check Podman is running:
```
podman version
```
Expected output: podman version 5.8.0

7) Check that you have no Podman virtual machines running
```
podman machine list
```
Expected output: no virtual machines running

8) Create a new Virtual Machine
```
podman machine init
```
> (note: if you face an error that Windows Subsystem for Linux is not installed, run wsl.exe --install and restart your system)

9) Start the virtual machine
```
podman machine start
```
10) Build a container image from the Containerfile in the current directory and tag it  scoreboard-demo
```
podman build -t scoreboard-demo .
```

10.1) Check that your image has been created
```
podman images
```
You will expect to see a localhost/scoreboard-demo image with teh tag of latest created minutes before 

11) Create and start your container from the image scoreboard-demo
* Running it in the background 
* Exposing it on port 8080 of your host.
```
podman run -d --name demo -p 8080:5000 scoreboard-demo
```
> The hexadecimal value returned is the container ID

11.1) Check that your container is running
```
podman ps
```
> Expected output is a single line with the image localhost/scoreboard-demo:latest with a Status of UP
> Rerunning the command should return the same result with an incremental status time.
> Confirming the container is running successfully

11.2) You can view the logs of your container by running
```
podman logs -f demo
```
12) Open in browser yor containerised application the browser:
[http://127.0.0.1:8000](http://127.0.0.1:8000)
> Depending on port forwarding, mixed results have been seen with the application displaying in the browser. 
> Continue to the next stage when ready

13) Stop the container
```
podman stop demo
```

13.1) Remove the container
```
podman rm demo
```
<code style="color : Red">🚀  Well Done you just containerised the application</code>
## Step 3 Pipelines & Git

14) Navigate to Github.com and login

15) Create a new repository
1. Select the hamburger menu top left 
2. Select Repositories
2. Select 'New repository' on the right hand side
3. Enter the repository name 
4. Keep provided defaults
5. Select 'Create repository' button

16) Return to Git bash and ensure that you are located within the DevOps-training-processes directory
Confirm location by running 
```
pwd
```
Expected output 
```
/c/Users/<user>/Desktop/DevOps-training-processes
```
17) To assist with running Git commands from the terminal, create a PAT (Personal Access Token) as this replaces a password and is the recommended authentication method
1. GitHub user Profile icon → Settings
2. Developer settings
3. Personal access tokens
4. Fine-grained token
5. Generate new token
6. Provide a token name
7. Under Repository access section select "Only select repositories" and from the drop down choose DevOps-training-processes
8. Under Permissions section, select +Add permissions and choose Actions, Contents & Workflows
9. For each permission change access to Read and Write
10. Generate token
7. Copy it immediately and make a note in a password manager for example

18) Add your new Github account remote
```
git remote add origin https://github.com/<your username>/DevOps-training-processes.git
```
> If this errors with 'remote origin already exists' a different user already exists. Run "git remote remove origin" and try again

19) Push the code to your new Github Account
```
git push -u origin main
```
> A pop up will appear requesting authentication. Choose Token tab and paste in your PAT token created above and sign in

Your code has now been pushed up to your Github Account

20) Log into your Github account and navigate to the new repository

20.1) Select Actions tab and observe under the Workflows section that an automated test has run. This was triggered by your push to Github

20.2) The Pipeline will now run a basic test and create an image of your application

20.3) On completion you should see a Green ticked workflow. Click into it to see the tests run image tar ball creation

<code style="color : Red">🚀  Well Done you just ran a pipeline with a test and image tar creation</code>

### Additional steps

If you want to pull down new changes from the source Github repo to your local version and then push up to your Github repo (Pull change from source User A > Local copy > Push up to User B)
1. Open local repo
2. Check current remotes (git remote -v)
3. Add User A as second remote (git remote add upstream https://github.com/userA/DevOps-training-processes.git)
4. Check current remotes (git remote -v). You will see 2 origin rows and 2 upstream rows
5. Fetch changes from userA (git fetch upstream)
6. Merge UserA changes into local branch (git checkout main)
7. Merge (git merge upstream/main) (if confliects run git add . then git commit)
8. Push up to UserB (git push origin main)
9. UserB workflow will now run
