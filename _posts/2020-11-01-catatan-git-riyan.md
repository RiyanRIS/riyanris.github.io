## GIT NOTES

### Introduction to git and github

#### Create a local git repository
- Create a directory for your project with the mkdir (make directory) command:

`mkdir myproject`

- Then open the directory with the cd (change directory) command.

`cd myproject`

- Now, initialize your new git repository in the folder with the git init command:

`git init`

#### Add a file to staging
- Add the file to the staging environment with the git add command:

`git add <file name>`

- Run the git status command again:

`git status`

Notice the "Changes to be committed" line. You will see that git added your file to the staging environment, but the file has not yet been added to a commit.

#### Create a commit
- Run the following, filling in any email for "you@example.com":

`git config --global user.email "you@example.com"`

- Run the following, filling in any name for "Your Name":

`git config --global user.name "Your Name"`

- Run the following git commit command. Your message should relate to what's in your commit - for posterity:

`git commit -m "<your message about the commit>"`


#### Create a new branch

- Since you're on the master branch already, run the git checkout -b command and name your branch. The command will:

    - Automatically create a new branch, using the name you specify
    - Immediately check the branch out to you
    - Move you to the new branch

`git checkout -b <branch name>`

- Confirm that your branch was created:

`git status`

- Switch back to the master branch.

`git checkout master`

- Make another file and add it:

`touch <file name>.txt`

`git add <file name>.txt`

- Use git commit -m "Your message about the commit" to stage your changes.

- Return to your branch.

`git checkout <branch name>`

Do you see any of the latest commits? You won't see the changes until you merge them into your new branch.


#### Push to [Github](https://github.com/riyanris) Repo

`git remote add origin <your GitHub repo URL>`
`git push -u origin master`


#### Push a branch to [Github](https://github.com/riyanris)

Pushing the commit in your branch to your new GitHub repo allows other people to see the changes you've made (think code review). The repository's owner can review changes prior to merging to the master branch.

- Run the following command to push changes onto a new branch on GitHub, replacing branch name with a name of your branch. Does GitHub automatically create the branch for you on the remote repository?

`git push origin <your branch name>`

- In GitHub, click on the Code tab. You should now see the branch name you just pushed.

- Click the Compare & pull request button that is next to your branch name.

- You'll now see the name of your commit. In a production environment you could leave a comment about this commit. For this lab click Create pull request.

- GitHub verifies that the files you are adding aren't in conflict with the Master copy. If everthing checks out, and it should since these are new files, you will see a green check mark. Click Merge pull request, then Confirm merge.

- You'll see a "Pull request successfully merged and closed" message. At this point you can click Delete branch to clean up. You don't have to do this, but you may end up with a mess if you have too many branches. Notice that you'll have another chance to leave comments.

#### Sync your local project with GitHub

Right now, the commit that you made in your branch then merged into the master branch doesn't exist in the version of Master on your local machine. Time to update your local version of Master.

- Use the git pull origin master command (specific to the master branch) to get the most recent changes from GitHub to your local repo.

`git pull origin master`

- Use the git log command to see all new commits.

`git log`

- Switch back to the master branch in your remote session. Use the git checkout master command:

`git checkout master`

- If you see the warning "Your branch is behind....", this means your local branch needs to add the commits from GitHub Master. Run the following:

`git checkout master`

- Then run the following to confirm everything is synced:

`git pull origin master`

- You should see a message that confirms your local repository is now up to date.

### PROBLEM

masalah pertama adalah 

`HEAD detached from 616f542`

ini ku temukan saat mengerjakan [project blog](https://s.id/riyan-blog).

terjadi ketika saya memiliki data `git log` seperti ini

![](https://i.ibb.co/SBWm2FK/image.png)

dikarenakan saya dengan ceroboh _--yang kemudian menjadi tahu_ melakukan `git checkout [sha]`.

ternyata perintah saya diatas adalah membuat branch baru bernama [sha].

yang tanpa saya sadari, pembaruan terus saya commit ke branch tersebut. sehingga branch master tertinggal di belakang branch [sha].

masalah terjadi ketika saya push dengan perintah `git push origin master` bilang up to date, padahal saya sudah melakukan beberapa pembaruan.

ahirnya saya browsing2 dan menjadi tahu ternyata **head** di local ada pada branch [sha].

solusi pertama adalah dengan melakukan perintah git push origin [sha]. namun ini harus digunakan terus karena branch terupdate adalah [sha]

solusi kedua adalah memindahkan branch [sha] ke branch master. degnan perintah `git branch -f master [sha]`

solusi kedua inilah yang saya gunakan. biar sesuai pada jalur. lalu saya cek `git log` sesuai dengan yang saya harapkan.





