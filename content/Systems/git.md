---
{"publish":true,"created":"03.08.2025 - 12:05","modified":"09.08.2025 - 17:40","tags":["dev","git","kb"],"cssclasses":""}
---

# GIT
## Doc

- [Git - Reference](https://git-scm.com/docs)
- [Git - Book](https://git-scm.com/book/en/v2)
- [Git Immersion](https://gitimmersion.com/)


## How to stop tracking and ignore changes to a file

- source:  [How to stop tracking and ignore changes to a file in Git? - Stack Overflow](https://stackoverflow.com/questions/936249/how-to-stop-tracking-and-ignore-changes-to-a-file-in-git)

- Just calling `git rm --cached` on each of the files you want to remove from revision control should be fine
- see also: [Git - Maintenance and Data Recovery](https://git-scm.com/book/en/v2/Git-Internals-Maintenance-and-Data-Recovery)

## Git update-index --skip-worktree, and how I used to hate config files

- source:  [Git update-index --skip-worktree, and how I used to hate config files](https://compiledsuccessfully.dev/git-skip-worktree/)

- Set flag

```bash
git update-index --skip-worktree <file_name>
```

- Remove flag

```bash
git update-index --no-skip-worktree <file_name>
```

##  Formatting (LF-style) and Whitespace 

- [Git - Git Configuration](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration#_formatting_and_whitespace)

## Reset to remote branch state

```bash
git fetch origin
git reset --hard origin/main
```

## Manage credentials

- see [[Systems/Windows#Manage Credentials]]

## Configuring a remote repository for a fork

- source:  [Configuring a remote repository for a fork - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/configuring-a-remote-repository-for-a-fork)

1. List the current configured remote repository for your fork.
    
    ```shell
    $ git remote -v
    > origin  https://github.com/YOUR-USERNAME/YOUR-FORK.git (fetch)
    > origin  https://github.com/YOUR-USERNAME/YOUR-FORK.git (push)
    ```
    
2. Specify a new remote _upstream_ repository that will be synced with the fork.
    
    ```shell
    git remote add upstream https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git
    ```
    
3. Verify the new upstream repository you've specified for your fork.
    
    ```shell
    $ git remote -v
    > origin    https://github.com/YOUR-USERNAME/YOUR-FORK.git (fetch)
    > origin    https://github.com/YOUR-USERNAME/YOUR-FORK.git (push)
    > upstream  https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git (fetch)
    > upstream  https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git (push)
    ```

## Syncing a fork

Sync a fork of a repository to keep it up-to-date with the upstream repository.

Before you can sync your fork with an upstream repository, you must configure a remote that points to the upstream repository in Git. For more information, see [Configuring a remote repository for a fork](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/configuring-a-remote-repository-for-a-fork).

1. Open Terminal.
    
2. Change the current working directory to your local project.
    
3. Fetch the branches and their respective commits from the upstream repository. Commits to `BRANCH-NAME` will be stored in the local branch `upstream/BRANCH-NAME`.
    
    ```shell
    $ git fetch upstream
    > remote: Counting objects: 75, done.
    > remote: Compressing objects: 100% (53/53), done.
    > remote: Total 62 (delta 27), reused 44 (delta 9)
    > Unpacking objects: 100% (62/62), done.
    > From https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY
    >  * [new branch]      main     -> upstream/main
    ```
    
4. Check out your fork's local default branch - in this case, we use `main`.
    
    ```shell
    $ git checkout main
    > Switched to branch 'main'
    ```
    
5. Merge the changes from the upstream default branch - in this case, `upstream/main` - into your local default branch. This brings your fork's default branch into sync with the upstream repository, without losing your local changes.
    
    ```shell
    $ git merge upstream/main
    > Updating a422352..5fdff0f
    > Fast-forward
    >  README                    |    9 -------
    >  README.md                 |    7 ++++++
    >  2 files changed, 7 insertions(+), 9 deletions(-)
    >  delete mode 100644 README
    >  create mode 100644 README.md
    ```
    
    If your local branch didn't have any unique commits, Git will perform a fast-forward. For more information, see [Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) in the Git documentation.
    
    ```shell
    $ git merge upstream/main
    > Updating 34e91da..16c56ad
    > Fast-forward
    >  README.md                 |    5 +++--
    >  1 file changed, 3 insertions(+), 2 deletions(-)
    ```
    
    If your local branch had unique commits, you may need to resolve conflicts. For more information, see [Addressing merge conflicts](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts).
    

>[!tip]
>Syncing your fork only updates your local copy of the repository. To update your fork on GitHub.com, you must [push your changes](https://docs.github.com/en/get-started/using-git/pushing-commits-to-a-remote-repository).

## Delete tag

source: [How To Delete Local and Remote Tags on Git – devconnected](https://devconnected.com/how-to-delete-local-and-remote-tags-on-git/)

### Delete a local Git tag

**In order to delete a local Git tag, use the “git tag” command with the “-d” option.**

```
$ git tag -d <tag_name>
```

For example, if you wanted to delete a local tag named “v1.0” on your commit list, you would run

```
$ git tag -d v1.0
Deleted tag 'v1.0' (was 808b598)
```

If you try to delete a Git tag that does not exist, you will simply be notified that the tag does not exist.

```
$ git tag -d v2.0
error: tag 'v2.0' not found.
```

If you want to make sure that tags were correctly deleted, simply list your existing tags using the tag command and the “-l” option.

```
$ git tag -l
<empty>
```

### Delete a remote Git tag

**In order to delete a remote Git tag, use the “git push” command with the “–delete” option and specify the tag name.**

```
$ git push --delete origin tagname
```

Back to the previous example, if you want to delete the remote Git tag named “v1.0”, you would run

```
$ git push --delete origin v1.0

To https://github.com/SCHKN/repo.git
 - [deleted]         v1.0
```

To delete a remote Git tag, you can also use the “git push” command and specify the tag name using the refs syntax.

```
$ git push origin :refs/tags/<tag>
```

Back to the example, in order to delete a tag named “v1.0”, you would run

```
$ git push origin :refs/tags/v1.0

To https://github.com/SCHKN/repo.git
 - [deleted]         v1.0
```

> Why should we specify the **“refs/tags”** instead of just specifying the tagname?

In some cases, your tag may have the same name as your branch.

If you tried to delete your Git tag without specifying the “[refs/tags](https://git-scm.com/book/en/v2/Git-Internals-Git-References)” you would get the following error

```
$ git push origin :v1.0

error: dst refspec v1.0 matches more than one.
error: failed to push some refs to '<repository>'
```

As a consequence, you need to specify that you are actually trying to delete a Git tag and not a Git repository.

## How do I delete a local branch in Git?

source: [How do I delete a local branch in Git? \| Learn Version Control with Git](https://www.git-tower.com/learn/git/faq/delete-local-branch)

Git makes managing branches really easy - and deleting local branches is no exception:

```
$ git branch -d <local-branch>
```

In some cases, Git might refuse to delete your local branch: when it contains commits that haven't been merged into any other local branches or pushed to a remote repository.  
This is a very sensible rule that protects you from inadvertently losing commit data.

If you want to delete such a branch nonetheless (e.g. because you've programmed yourself into a dead end and produced commits that aren't worth keeping) you can do so with the "-D" flag:

```
$ git branch -D <local-branch>
```

This will force deletion of the branch, even if it contains unmerged / unpushed commits. It goes without saying: _please be careful with this command!_

## How do I delete a remote branch in Git?

To delete a _remote_ branch, you need to use the "git push" command:

```
$ git push origin --delete <remote-branch-name>
```