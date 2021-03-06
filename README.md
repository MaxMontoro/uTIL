# uTIL
**Micro TILs and utilities for macOS**

---

## Terminal commands

### Network

Flush DNS  
`sudo dscacheutil -flushcache`

Edit hosts file (to manually override DNS servers)  
`sudo vim ./etc/hosts`

Start a simple HTTP server with Python   
`python -m http.server` (Python3) or `python -m SimpleHTTPServer` (Python2)

Run MongoDB as a backround service  
`mongod --fork --config /usr/local/etc/mongod.conf`

See which process is using a specific port  
`lsof -i tcp:[port nr.]`

### Troubleshooting

List applications by memory usage  
`top -o MEM`

List disk I/O and CPU load  
`iostat`

List directories, sorted by size  
`du -s * | sort -nr`


### Diff

`vim -d file1 file2`  see diff between two files in Vim

`git diff file1 file2` . use Git for the same thing

`diff file1 file2` . use plain old `diff` for the same thing

### Misc

Run last command as root  
`sudo !!`

Go to home dir
`cd`

Function that looks for a specific string in the current directory, listing its occurences with 5 lines of context before and after it, and highlights the search term. 
You can put this into your `bash_rc` or `bash_profile` for the magic. 
```
gr () {
    grep -F -r -A 5 -B 5 "$1" . --color=auto
}
```

Edit aliases, PATH and other environment variables  
`sudo vim ~/.bash_profile`  
`source ~/.bash_profile`  

Set aliases directly from the command line  
`alias wikisurf="python ~/WikiSurf/WikiSurfPy.py"`

Show size of directory (including subdirs and files)  
`du -sh [path/to/dir]`

Find occurences of a word in files matching a regex pattern. Uses **ack**.  
`find adir -regex ".*pattern_here.*.py" | ack --files-from=- search_term_here` . 

Find and replace string in current directory:  
`find . -type f -print0 | xargs -0 perl -pi -w -e 's/oldstring/newstring/g;'`

In Ubuntu, turn off line wrap with the following command:  `tput rmam`. The output of any command will be chopped off at your  current terminal width.
Turn it back on with `tput smam`. 

### Python

Open Python script in interactive mode (runs script and stays in the interpreter, preserving namespace)  
`python -i [filename]`

Use the Python Debugger to interactively debug your code:  
`python -m pdb script.py`

Create a virtual environment and activate it  
`python -m venv path/to/venv`  
`source path/to/venv/activate`  

If the above command fails with something like  ` '-Im', 'ensurepip', '--upgrade', '--default-pip']' returned non-zero exit status 1`, try the following :   
`python -m venv --without-pip path/to/venv`    
`source path/to/venv/bin/activate`  
`curl https://bootstrap.pypa.io/get-pip.py | python`  
`deactivate`  
`source path/to/venv/bin/activate`  

Shebang line for Python 3  
`#!/usr/bin/env python3`

#### Python debugger commands  
`import pdb; pdb.set_trace()` or `breakpoint()` (Python 3.6+) to a part of your code you want to inspect 

`sticky` to see the code context around the breakpoint

`n` for executing next line

`c` for continueing until next breakpoint

`s` for stepping inside function call 

`interact` to interact with the interpreter

`pp <expression>` to pretty-print a variable (for instance, `pp vars(an_object)` or `pp an_object.__dict__` can be very handy)

## Git (CLI)

#### Workflow

*This works for basic setups, however, the recommended workflow should always be discussed with the team*

1. (optional) Get the latest version of the branch from remote with `git fetch` (does not merge!)  
2. Get & merge the latest version of the remote branch with `git pull`  
3. Create a branch to work on & move to the new branch with `git checkout -b <branch>`  
4. Work and commit as usual  
5. Pull and merge the originating branch to avoid merge conflicts  
6. Create pull request  


##### Get info

`git branch`  
shows existing branches & which branch we are working on

`git branch -a`  
shows remote branches too  

`git branch --sort=committerdate`  
shows your local branches sorted by their last commit date (find your recent branches this way)

`git s` or `git status`  
shows files changed since last commit & whether they are included in the next commit  

`git diff`  
shows the changes in each file compared to the last commit  

`git diff HEAD^`  
shows difference between each file and the parent of the last commit (useful to check what you are about to push!)

`git diff <SHA1> <SHA2> --name-only` or `git diff <branch1> <branch2> --name-only`  
shows names of files that differ in those commits/branches

`git blame <file>`  
shows who edited each line  

`git show HEAD`  
shows the last commit (author, date, message, diff)

`git show <commit-sha>`  
shows changes introduced by the commit

`git show --no-prefix -U1000 <commit-sha>`  
shows changes introduced by the commit, with full context

`git log -S <string> path/to/file`  
Git blame for a deleted line

`git log --follow path/to/file`   
Git log but only for that file.


##### Move around

`git checkout <branch>`  
move to another branch  

`git checkout -b <branch>`  
move to another branch, and if it doesn't exist, create a new one

`git checkout <commit hash>`
check out a snapshot corresponding to a specific commit. This creates a detached head (not referring to any branch).

##### Add things

`git add <file or path>`  
add a file or path to be included in the next commit (wildcards work for paths too, e.g. `git add src/scripts/*`)  

`git add .` or `git add -A` or `git add --all`  
add everything  

`git cherry-pick SHA`  
apply a commit from another branch to the current one

`git reset HEAD <file>`  
revert changes in a file to last commit

`git commit --amend`   
add some more changes to the previous commit

`git reset --soft HEAD@{1}`   
undo erroneously amended commit


##### Fetching, merging, pulling

`git fetch`  
get recent changes from remote

`git pull`  
fetch and merge changes (prone to conflicts!)

`git pull --rebase`  
Rebase the current branch on top of the upstream branch after fetching. Useful if working with a master-only workflow and you want to maintaing linear history.

##### Clean up

`git branch -D <branch name>`  
delete branch

`git add -u :/`  
remove locally deleted files from remote (files that you already deleted from disk)


##### Stash (cut & paste)

`git stash`  
remove all changes since last commit & put them on a clipboard (useful e.g. if you want to merge master before a PR)  

`git stash -k`  
stash but only the unstaged changes (i.e. those which were not `git add`-ed)  

`git stash show -p`  
view the contents of the stash (looks like a git diff)  

`git stash pop`  
apply the stashed changes again  

`git diff stash@{0}^1 stash@{0} -- <filename> | git apply`  
only apply the changes from one specific file from the stash  

`git stash list`  
show the contents of the stash (pop pops the latest, `stash@{0}`)  

`git stash clear`  
empty the stash

#### Extras & Extensions

##### Shortcuts

- Set short aliases to frequently used commands, e.g. `git chdev` = `git checkout develop`

        git config --global alias.chdev 'checkout develop'

- List existing aliases

        git config --global -l


##### Preview Github Readme.md file locally
  - Install [grip](https://github.com/joeyespo/grip): `$ pip install grip`
  - In the folder of the repo, type `$ grip`
  - Visit `http://localhost:6419/` in your browser

##### Generate an SSH Public Key

1. Check if an SSH key pair exists already

        $ cd ~/.ssh
        $ ls

  If `<key_name>` and `<key_name>.pub` are there, skip to step 3.

2. If no key found, generate one

        $ ssh-keygen -t rsa -C "your.email.used.on.github@example.com"

3. Check the contents of the public (`.pub`) key

        $ cat <key_name>.pub

4. Copy the all of the contents of the public key, go to [Github > Settings > SSH and GPG keys](https://github.com/settings/keys), click 'New SSH key' and paste the contents of the public key.

To generate multiple keys with different names:  
`ssh-keygen -t rsa -f /path/to/key`

## Image manipulation with Imagemagick

#### How to create a favicon.ico

        $ brew install imagemagick
        $ magick input.png -resize 32x32 favicon.ico

## Etc
### Show hidden files in macOS Finder
press `cmd`+`shift`+`.`
