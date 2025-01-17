---
title: "Looking at history and differences"
teaching: 30
exercises: 15 (inc 10 for break)
questions:
- "How do I get started with Git?"
- "Where does Git store information?"
objectives:
- "Be able to view history of changes to a repository"
- "Be able to view differences between commits"
- "Understand how and when to use tags to label commits"
keypoints:
- "`git log` shows the commit history"
- "`git diff` displays differences between commits"
- "`git checkout` recovers old versions of files"
- "`HEAD` points to the commit you have checked out"
- "`master` points to the tip of the `master` branch"
---


### Looking at differences

We should [reference some previous work][reference-previous-work] in the introduction section.
Make the required changes, save both files but do not commit the changes yet.
We can review the changes that we made using:

~~~
$ gedit paper.md		# Cite previous studies in introduction
$ gedit refs.txt		# Add the reference to the database
$ git diff			# View changes
~~~
{: .language-bash}

This shows the difference between the latest copy in the repository and the
unstaged changes we have made.

* `-` means a line was deleted.
* `+` means a line was added.
* Note that a line that has been edited is shown as a removal of the old line and an
addition of the updated line.

Looking at differences between commits is one of the most common activities.
The `git diff` command itself has a number of [useful
options](http://git-scm.com/docs/git-diff.html).

There is also a range of GUI-based tools for looking at differences and
editing files. For example:

* [Diffmerge](https://sourcegear.com/diffmerge/) (Free, cross-platform)
* [WinMerge](http://winmerge.org/) - open source tool available for Windows;
* GitHub [Compare
view](https://help.github.com/articles/comparing-commits-across-time)

Git can be configured to use graphical diff tools, and this is functionality
is accessed using `git difftool` in place of `git diff`.
Configuring a visual diff tool is covered on the
[hints and tips](11-hints-and-tips.html) page.
The choice of GUI for viewing differences depends on the context in which you
are working and your own preferences related to choosing tools and
technologies.

Now commit the change we made by adding the second reference:
```
$ git add paper.md refs.txt
$ git commit			# "Cite previous work in introduction"
```
{: .language-bash}

### Looking at our history

To see the history of changes that we made to our repository (the most recent
changes will be displayed at the top):

~~~
$ git log
~~~
{: .language-bash}

```
commit 8bf67f3862828ec51b3fdad00c5805de934563aa
Author: Your Name <your.name@manchester.ac.uk>
Date:	Mon Jun 26 10:22:39 2017 +0100

    Cite PCASP paper


commit 4dd7f5c948fdc11814041927e2c419283f5fe84c
Author: Your Name <your.name@manchester.ac.uk>
Date:	Mon Jun 26 10:21:48 2017 +0100

    Write introduction

commit c38d2243df9ad41eec57678841d462af93a2d4a5
Author: Your Name <your.name@manchester.ac.uk>
Date:	Mon Jun 26 10:14:30 2017 +0100

    Add author and title
```
{: .output}

The output shows (on separate lines):
- the commit identifier (also called revision number) which
uniquely identifies the changes made in this commit
- author
- date
- your commit message

Git automatically assigns an identifier (e.g. 4dd7f5) to each commit
made to the repository
--- we refer to this as *COMMITID* in the code blocks below.
In order to see the changes made between any earlier commit and our
current version, we can use  `git diff` followed by the commit identifier of the
earlier commit:

~~~
$ git diff COMMITID		# View differences between current version and COMMITID
~~~
{: .language-bash}

And, to see changes between two commits:

~~~
$ git diff OLDER_COMMITID NEWER_COMMITID
~~~
{: .language-bash}

Using our commit identifiers we can set our working directory to contain the
state of the repository as it was at any commit. So, let's go back to the very
first commit we made,

~~~
$ git log
$ git checkout INITIAL_COMMITID
~~~
{: .language-bash}

We will get something like this:

~~~
Note: checking out '21cfbdec'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 21cfbde... Add title and authors
~~~
{: .output}

This strange concept of the 'detached HEAD' is covered in the next section ...
just bear with me for now!

If we look at `paper.md` we'll see it's our very first version. And if we
look at our directory,

~~~
$ ls
~~~
{: .language-bash}
~~~
paper.md
~~~
{: .output}

then we see that our `refs.txt` file is gone. But, rest easy, while it's
gone from our working directory, it's still in our repository. We can jump back
to the latest commit by doing:

~~~
$ git checkout master
~~~
{: .language-bash}

And `refs.txt` will be there once more,

~~~
$ ls
~~~
{: .language-bash}
~~~
paper.md refs.txt
~~~
{: .output}
So we can get any version of our files from any point in time. In other words,
we can set up our working directory back to any stage it was when we made
a commit.

### The `HEAD` and `master` pointers

*HEAD* is a reference, or pointer, which points to the branch at the commit where
you currently are.
We said previously that `master` is the default branch. But `master` is
actually a pointer - that points to the tip of the `master` branch (the sequence
of commits that is created by default by Git). You may think of `master` as two
things:

1. a pointer
2. the default branch.

Before we checked out one of the past commits, the *HEAD* pointer was pointing to
`master` i.e. the most recent commit of the `master` branch.
After checking out one of the past commits, *HEAD* was pointing to that commit i.e.
not pointing to master any more.
That is what Git means by a 'detached HEAD' state and advises us that if we want to make a commit
now, we should create a new branch to retain these commits.

![Checking out a previous commit - detached head](../fig/detached-head.svg)

If we created a new commit without first creating a new branch, i.e. working from
the 'detached HEAD' these commits would not overwrite any of our existing work,
but they would not belong to any branch. In order to save this work, we would need
to checkout a new branch. To discard any changes we make from the detached HEAD
state, we can just checkout master again.

## Visualising your own repository as a graph
If we use `git log` with a couple of options, we can display the history as a graph,
and decorate those commits corresponding to Git references (e.g. `HEAD`, `master`):

~~~
$ git log --graph --decorate --oneline
~~~
{: .language-bash}

```
* 6a48241 (HEAD, master) Cite previous work in introduction
* ed26351 Cite PCASP paper
* 7446b1d Write introduction
* 4f572d5 Add title and author
```
{: .output}

Notice how `HEAD` and `master` point to the same commit.
Now checkout a previous commit again, and look at the graph again.
We can display, this time specifying that we want to look at `--all` the history,
rather than just up to the current commit.

```
$ git checkout HEAD~				# This syntax refers to the commit before HEAD
$ git log --graph --decorate --oneline --all
```
{: .language-bash}

```
* 6a48241 (master) Reference second paper in introduction
* ed26351 (HEAD) Reference Allen et al in introduction
* 7446b1d Write introduction
* 4f572d5 Add title and authors
```
{: .output}

Notice how `HEAD` no longer points to the same commit as `master`.
Let's return to the current version of the project by checking out `master` again.

```
$ git checkout master
```
{: .language-bash}

### Using tags as nicknames for commit identifiers

Commit identifiers are long and cryptic. Git allows us to create tags, which
act as easy-to-remember nicknames for commit identifiers.

For example,

```
$ git tag PAPER_STUB
```
{: .language-bash}

We can list tags by doing:

```
$ git tag
```
{: .language-bash}

Let's explain to the reader [why this research is important][give-context]:

```
$ gedit paper.md	# Give context for research
$ git add paper.md
$ git commit -m "Explain motivation for research" paper.md
```
{: .language-bash}

We can checkout our previous version using our tag instead of a commit
identifier.

```
$ git checkout PAPER_STUB
```
{: .language-bash}

And return to the latest checkout,

```
$ git checkout master
```
{: .language-bash}

> ## Top tip: tag significant events
> When do you tag? Well, whenever you might want to get back to the exact
> version you've been working on. For a paper, this might be a version that has
> been submitted to an internal review, or has been submitted to a conference.
> For code this might be when it's been submitted to review, or has been
> released.
{: .callout}

> ## Where to create a Git repository?
> Avoid creating a Git repository within another Git repository.
> Nesting repositories in this way causes the 'outer' repository to
> track the contents of the 'inner' repository - things will get confusing!
{: .callout}

> ## Exercise: "bio" Repository
>
> - Create a new Git repository on your computer called "bio"
> - Be sure not to create your new repo within the 'paper' repo (see above)
> - Write a three-line biography for yourself in a file called **me.txt**
> - Commit your changes
> - Modify one line, add a fourth line, then save the file
> - Display the differences between the updated file and the original
>
> You may wish to use the faded example below as a guide
>
> ```
> cd ..                # Navigate out of the paper directory
>                      # Avoid creating a repo within a repo - confusion will arise!
> mkdir ___            # Create a new directory called 'bio'
> cd ___               # Navigate into the new directory
> git ____             # Initialise a new repository
> _____ me.txt         # Create a file and write your biography
> git ___ me.txt       # Add your biography file to the staging area
> git ______           # Commit your staged changes
> _____ me.txt         # Edit your file
> git ____ me.txt      # Display differences between your modified file and the last committed version
> ```
> {: .language-bash}
>
> > ## Solution
> >
> > ```
> > cd ..                # Navigate out of the paper directory
> >                      # Avoid creating a repo within a repo - confusion will arise!
> > mkdir bio            # Create a new directory
> > cd bio               # Navigate into the new directory
> > git init             # Initialise a new repository
> > gedit me.txt         # Create a file and write your biography
> > git add me.txt       # Add your biography file to the staging area
> > git commit           # Commit your staged changes
> > gedit me.txt         # Edit your file
> > git diff me.txt      # Display differences between your modified file and the last committed version
> > ```
> > {: .language-bash}
> {: .solution}
{: .challenge}

[reference-previous-work]: https://github.com/gcapes/git-course-paper/commit/add59ba7d0b963531195eb3774b79bb9bcd749d1#diff-0403ef06adf405f7b310b4518bd6a3559854f54c61676f676ce9cbfee7172ab6
[give-context]: https://github.com/gcapes/git-course-paper/commit/92f674de54c77afd7ae32342f9f03d2bdd8fb76d#diff-0403ef06adf405f7b310b4518bd6a3559854f54c61676f676ce9cbfee7172ab6

{% include links.md %}
