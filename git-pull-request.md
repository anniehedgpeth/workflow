# Setup 
(ONLY DO THIS ONCE PER REPO - EVER)

1. Fork the repository for which you want to create a *pull request*
2. Clone the repository locally. **This will be your `origin` remote.** ```git clone <url>```
3. Tell git in the command line where the original source came from. **This will be your `source` remote.**  ```git remote add source <cloned-url-of-original-source-repo>```
4. Check that you have everything set up correctly. **This should list the forked (`origin`) repository and the original (`source`) repository.**  ```git remote -v```

# Editing the Repo 
(MAY DO THIS MULTIPLE TIMES PER PULL REQUEST)

1. Make sure you're on the `master` branch  ```git checkout master```
2. Make sure your local repo is synced with the source repo  ```git pull source master```
3. Make sure your forked repo (`origin`) is synced with your local repo  ```git push origin master```
4. Create a branch, name it, and switch to it  ```git checkout -b <branch-name>```
5. Make your changes to the repo and save
6. Commit your changes in Visual Studio Code (use good commit language)
7. Publish

# Create a Pull Request
(DO THIS ONCE PER PULL REQUEST)

1. Go to my github forked repo (`origin`) in a web browser
2. Click on "Branch" drop down menu to change to your branch
3. Click "New Pull Request" button (next to "Branch" button)
4. Fill out form and submit
