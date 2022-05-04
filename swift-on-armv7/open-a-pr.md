# Opening a PR (pull request)
#### Step 1 - Create a Fork of the Repository
First we need to create a fork of the repository.

![](images/pr/fork-repo.png)

![](images/pr/fork-repo-2.png)

#### Step 2 - Checkout Branch
The branch for `swift on armv7` is `debian/bullseye-5.6-armv7`  
Select this branch in the branches drop down menu.

![](images/pr/checkout-branch.png)

#### Step 3 - Create New Branch
Next we need to create a new branch for our changes e.g. `my-awesome-update` based on the `debian/bullseye-5.6-armv7` branch.

![](images/pr/new-branch.png)

#### Step 4 - Make Some Changes
All changes we make will be done in this new branch `my-awesome-update`.  
For this example we will add a simple comment to the README file.

![](images/pr/make-change.png)

#### Step 5 - Commit Changes
Save the changes by committing to the `my-awesome-update` branch.  

![](images/pr/commit-changes.png)

#### Step 6 - Open a Pull Request
On your repository front page tap the `Compare and pull request` button to open a pull request.  

![](images/pr/create-pull-request.png)

NOTE: Make sure you select the correct repository and branches.  
When the details are correct tap the `Create pull request` button.

![](images/pr/choose-branch.png)

![](images/pr/create-pr.png)


#### Step 7 - Authorising PR Test Builds
The first time you submit a PR it will need to be authorized.  
There will be an automated response `You need to be authorised to automatically run #PR builds.`  

![](images/pr/get-authorised.png)

Once you have the `ok to test` the PR test build will start.

![](images/pr/ok-to-test.png)

You can check the progress of the build on the `Jenkins Dashboard`.

![](images/pr/build-running-dashboard.png)



