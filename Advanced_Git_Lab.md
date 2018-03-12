# Basics

## Structure

### How is Git structured on the backend?

#### Commits

If you've used Git at all before, you're probably familiar with the idea that it uses _commits_ to track changes.
Or maybe it tracks files? 
Well it tracks something, right?
I mean, that's how it does\ldots whatever it is that is actually does.

Many other version control systems (VCS) store changes as files and the changes made over time (these types of systems are referred to as _delta-based_.)(Figure \ref{delta})

![A delta-based VCS history. \footnotesize{ProGit}\label{delta}](./img/delta-based.png)

Although many people think of it this way (I did until I put together this document), Git is __not__ delta-based.
Instead, it stores snapshots of the current state of each file, while unchanged files are represented by a link to the previous identical file. (Figure \ref{snapshot-based}) (TODO: does this mean a link to a link or multiple links to one real file?)

![A snapshot-based VCS (Git) history. \label{snapshot-based}](./img/snapshot-based.png)

Everything in Git (including commits) is hashed using SHA-1 and stored by this hash value.

#### The three stages of Git

Or more accurately, the three areas of Git. 
The _.git directory_ is where the metadata and object database for a repository is stored, and is what is actually copied when cloning a project (along with the files themselves.)
The _working tree_ is a checked-out version of the project. 
These are the files themselves which are actually modified by the user.
The _staging area_ (or _index_) is a file in the .git directory which stores information about what will go into the next commit.

# Branching

- Management
    - Video on team management
- Merging
- Rebasing
- Checkout

# Remote Management

- Pulling
- Pushing
- Forcing
- Merging with remote
- GitHub advance (pull requests etc.)

# Misc

## Searching/Logging

Git provides a powerful set of tools for searching through both the working directory and history.

### `git grep`

Analogous to the ubiquitous command-line tool `grep`, `git grep` defaults to searching all files in the working directory.

#### Examples

`git grep STRING`: Returns a list of the file names and lines containing `STRING`.

##### Flags

`-E` -- Search with extended regexes
`-n` -- Return line numbers with search results.
`-c` -- Return a count of occurrences of `STRING` by file.
`--untracked` -- Search all files in the working directory, even if they are not managed by Git.

##### References

[`git grep` documentation](https --//git-scm.com/docs/git-grep)

### git log

Of course, the primary feature of Git is having a record of past versions of the file, so it would be nice to be able to search through the commit history.
`git log` does just this.
Running `git log` will produce a list of all commits with associated hashes, author information, datetimes, and messages.
You _could_ pipe this output into `grep` and find specific information that way, but thankfully there is a better way.
In fact, there are multiple better ways depending on exactly what you want to do.

#### Examples

`git log [FILE] [BRANCH]` -- Return a list of all commits for the selected file(s) or branch(es)

##### Flags

`-S STRING` -- Return a list of commits which changed the number of occurrences of `STRING`. Passing a `FILE` or `BRANCH` will limit searching to those areas.
`-L START,END:FILE` -- Return a list of changes to a specified range of lines in `FILE`.
`-L :FUNCTION:FILE` -- Return a list of changes to a specified `FUNCTION` in a `FILE`. Git is smart enough to recognize function boundaries in most popular languages.
`-n NUMBER` -- Limit the number of commits to show.

###### Cosmetic options

There are a number of options to "prettify" the output of `git log` into something a little more user friendly.
Many other options exist beyond these, but this is the what I use. 

`--graph` -- Output a network graph of the branches and commits (think GitHub's Network display)
`--decorate` -- Print the ref names of commits.
`--pretty=oneline` -- Pretty-prints the commit data into one line. Can pass a number of options for formatting, including `--pretty=oneline`, `short`, `medium`, `full`, `fuller`, and `raw`, in increasing order of information.
`--abbrev-commit` -- Only print the partial hash for each commit.

##### Reference

[`git log` documentation](https://git-scm.com/docs/git-log) 


- Blaming
- Diffing
- Reset vs revert
- Stashing
- Interactive staging

# Configuration

## .gitconfig/`git config`

Modifying options for Git is as simple as editing the `/etc/.gitconfig` file.
Or editing the `~/.gitconfig` file. 
Or editing the `.git/config` file for the working directory. 
Or running `git config`. 
Or running `git config --global`. 
Or\ldots

Ok, you get the picture. 
There's quite a few places that Git stores configuration settings, and quite a few ways to modify them.
There are three levels of configuration that can be set for Git.
In decreasing order of precedence, there is the `.git/config` file in the project directory itself.
Below that is the `~/.gitconfig` file, which controls configuration on the user level, and `/etc/.gitconfig`, which controls the system level settings for all users.
These files can be edited as basic text files, or modified with the `git config` command.
For simplicities sake, I will ignore the `config` command, as it is essentially a less intuitive way to edit the files manually.
Let's look at some of the options.

### Aliases

Given the massive number of flags and options available for each of the many Git commands, being able to alias common commands to more easily remembered commands is very helpful.
If you wanted to view a nice, pretty history of your current project, like Figure \ref{git-la}, Which would you rather type?

`git log --graph --decorate --pretty=oneline --abbrev-commit --all`?

Or `git la`?

![Pretty git network graph.\label{git-la}](./img/git-la.png)

The syntax for these is pretty straightforward.
In whichever `.gitconfig` file, under the heading `[alias]`, add a line of the form

`<shortcut> = <long git command with flags>`

For the `git la` example above, the entry in my `.gitconfig` file reads

```
[alias]
    la = log --graph --decorate --pretty=oneline --abbrev-commit -all
```

### User information

While it seems likely that most aliases will be added to the user-level configuration, the tiered system allows for fine control over user information like names and emails.

Under the `[user]` heading, information like `name = <My name>` or `email = <whatever address to use for the current project>` can be modified.

### Auto-rebase on pull

This is (admittedly) a preference, but adding

```
[pull]
    rebase = true
```

(or using `git pull --rebase`) will automatically perform a rebase when pulling from remote where you have unique local changes.
By default, Git will merge these changes, resulting in the infamous merge bubble:

![Example of a merge bubble.](./img/merge-bubble.png){width=100px}

However, automatically rebasing when pulling would instead have given a nice, uninterrupted string of green dots.
For a discussion of why you might __not__ want to do this, there is a useful [__StackExchange__](https://softwareengineering.stackexchange.com/questions/307063/why-does-git-pull-perform-a-merge-instead-of-a-rebase-by-default ) discussion on the topic.

### .gitignore

Additionally, if you create a file `~/.gitignore`, it will act as a default list of ignored paths for all projects.
This is great for common editor temporary files (`*.swp` for vim or `.#*` for emacs), common binary extensions, archives etc.

### Examples

A great place to find Git configuration files is, where else, GitHub! 
[pksunkara](https://gist.github.com/pksunkara/988716) has a great set of `.gitconfig` and `.gitignore` files which really show off the flexibility of Git.
Many users keep their dotfiles, `.gitconfig` included, on GitHub, so searching for these can give good inspiration.

## Git/GitHub integration into text editor

# Further reading

- References
