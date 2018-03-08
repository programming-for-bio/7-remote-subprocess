## Code-Review

See the table at the bottom of this README for your assigned repo to review. 
If the person you are assigned to has not completed their assignment by the 
time you start your review (after 5pm on Friday) then you can choose any 
random other repo to review. 


## Instructions


### 1. Connect to the HPC cluster
For this code-review you will be reviewing the other user's code by working 
on the HPC cluster, pulling in their assignment file, and working on it 
remotely. Then you will push the changes back to your repo and make a pull request to the class remote. 

The `git` commands required to do this are bit more complex than we have used
so far, but I'll explain each step as we run it and we should encounter no 
problems. Let's start by connecting to the cluster using our shorthand 
command we set up in class. 

```bash
ssh habanero
```

### 2. cd into your 7-remote-subprocess repo directory
You should have already cloned **your** forked 7-remote-subprocess repo during 
your assignment. If not, clone it now, and cd into that directory. For me the path is `~/PDSB/7-remote-subprocess`, but you may have saved it in a slightly different place. 

Now that you are in your repo, we are going to create a new separate branch to work on for the code review, and on this branch you'll `pull` in changes in the
form of all of the submitted assignments that have been pulled into the course
version of the repo. We can pull in these changes simply by entering the 
address of the course git repo and the name of the requested branch (master).
You will then have access to all of the assignment files.


```bash
## cd into your 7-remote-ssh git repo
cd PDSB/7-remote-ssh/

## create and switch to a new branch named code-review
git checkout -b code-review

## pull the submitted Assignments from the course repo into this branch
git pull https://github.com/programming-for-bio/7-remote-subprocess master
```
 
It wil then print a message similar to below, but with more files updated 
in it. This is telling you which files were added to the repo, or changed 
in it relative to your version. The course remote has everyones' assignments
on it so you are mostly pulling in those assignment files.
```
[de2356@watson testdir]$ git pull https://github.com/programming-for-bio/7-remote-subprocess master
remote: Counting objects: 39, done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 39 (delta 18), reused 26 (delta 9), pack-reused 0
Unpacking objects: 100% (39/39), done.
From https://github.com/programming-for-bio/7-remote-subprocess
 * branch            master     -> FETCH_HEAD
Updating c183a46..91f5f83
Fast-forward
 Assignment/kuratanp-7.3.ipynb | 275 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 Assignment/mvanack-7.3.ipynb  | 275 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 Code-Review/README.md         | 254 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 Lecture/PDSB-Lecture-7.pdf    | Bin 0 -> 1575109 bytes
 4 files changed, 804 insertions(+)
 create mode 100644 Assignment/kuratanp-7.3.ipynb
 create mode 100644 Assignment/mvanack-7.3.ipynb
 create mode 100644 Lecture/PDSB-Lecture-7.pdf
```

You will now have the files from the other users' repositories.

## check that assignment files are now in your Assignment/ dir
```bash
ls -l Assignment/
```


### 3. Make a copy of your assigned Assignment notebook and rename it

Please rename the file exactly as instructed below. Take note that you are 
changing the file name in the new copy so that instead of the other user's 
github username it will have **your** github username in the file name. 
Choose the notebook 7.2 file for your assigned student review, but if that student's assignment is missing then choose any other notebook 7.2 from the Assignment directory to review. 

```bash
cp Assignment/<their-username>-7.2.ipynb Code-Review/<your-username>.ipynb
```

#### 4. Start a jupyter-notebook server to test their code 
Submit an `sbatch` job to start a jupyter-notebook server running on a compute
node and connect to it so that you can test their code running on Linux. You 
can use the sbatch script from our shared scratch space by entering the 
command below, which will start a job running for 1 hour.  

```bash
## submit job request
sbatch /rigel/edu/w4050/files/jupyter-edu-one-hr.sbatch
```

To check whether the job has started yet you can run the command 
`squeue -u <uni>` or, a cool unix trick to get this command to repeat every N 
seconds so that you can sit back and watch until the job starts is to use the 
`watch` command like below. This will print the output of the command updated
every 1 second. When it starts you can press control+c to stop it 
(the exact keys might be different on osx or windows, e.g., 
`<windows-key>`+c, I'm not sure, but you'll figure it out). 

```bash
## print whether the job has started every 1 second
watch -n 1 'squeue -u de2356'
```

Once you see that the job has started running, follow the instructions that
will be printed in the job's output file (written to the output directory), 
as we did in notebook 7.3, to setup an SSH tunnel so that you can connect to 
the notebook through a browser on your laptop. To find the file in the ouputs
directory that was most recently generated you can use the `ls` command with the
arguments `-ltr`, meaning print as a list and sort by time and reverse the order. 

```bash
## list files in the directory reverse time sorted
ls -ltr ~/outputs/
```

#### 5. Open the code review notebook
Since we made a copy of the notebook we can edit this version as much as we
want without worrying about overwriting that persons assignment file. 
We will be use this copied file to write our code reviews. From the jupyter
notebook server open in your browser (e.g., from localhost:xxxx where xxxx is
the port number listed in the output file), open the code review notebook file 
that you created in
`~/PDSB/7-remote-subprocess/Code-Review/<your-username>.ipynb`. 

#### 6. Test their code
In the toolbar at the top of the notebook select the tab named `Cell`, and 
then select the option `Run All`. This should execute the full notebook 
successfully, including importing all libraries, storing variables like 
"fasta_data" and "fasta_string", and loading the `Phylogeny` Class object 
that the student wrote and running it.


#### 7. Code Review
Create new cells at the bottom of the notebook separated by a large header in 
Markdown to label the section `Code-Review`. Then, using markdown answer the 
questions below in your notebook by copying the question into a cell and 
clearly writing you answer to it. Start you answer by writing **Yes or No** to each question, followed by further explanation after following the instructions for each question.


**1. Does the run function fill all attributes?**.   
The instructions asked that "`all of the attribute variables in __init__ (e.g., self.aligned) are filled by functions called during the .run() function.`". Copy the code below into a code cell and run it to test whether all of the attributes of the instance are changed to be not None after `run` is called. 

```python
p = Phylogeny(fasta_string)
p.run(outname="code-review")

for attribute in [p.fasta, p.aligned, p.phylip, p.tree, p.log]:
    if attribute is None:
    	print("the attribute {} should have been filled".format(attribute))
```


**2. Does the `_run_raxml` function remove previous run files?**.  
The instructions asked that "`the _run_raxml() function removes an existing output file with the same name if it exists.`". Look at the code for the `_run_raxml` function. Is there a conditional statement, or any kind of check for whether  an output file already exists using the given outname argument? If so, explain how they did it, and how this compares to your implementation.



**3. Does their code modify the raxml command to set the name of the output 
file?**.  
The instructions asked that "`the _run_raxml() function modifies the command for raxml by replacing the argument '-n out' with '-n <outname>', and then running it with subprocess and parsing the result.`".  
Again, look at the code in `_run_raxml`, do they include the outname
variable in this command after the `-n` flag? Run the code below to check 
whether a file is created using the outname variable. 

```python
p = Phylogeny(fasta_string)
p.run(outname="code-review")

import os
os.path.exists("./RAxML_bestTree.code-review")
```

**4. Does the `_run_raxml` function parse the RAxML_bestTree file as a
string?**  
The instructions asked that "`the _run_raxml() function parses the 
result from the raxml output file RAxML_bestTree.<outname> as a string and 
returns it.`". To parse the file as a string means to open and read the 
content of it and store the results as a string variable. In the skeleton 
code that I provided it was implied that this should be stored in `self.tree`.
The following code will test if `self.tree` matches the content of the output
file. 


```python
p = Phylogeny(fasta_string)
p.run(outname="code-review")

# open the bestTree file and compare it to self.tree as string data
with open("./RAxML_bestTree.code-review", 'r') as treedata:
    print(p.tree == treedata.read())
```


### 8. Submit your Code Review

So, to recap on our `git` situation, we created a new branch on which we pulled
in committed changes that other students had made to their code and which had 
been merged into the course repo using pull-requests. When we pulled in these 
changes to our new branch the files on that branch were modified, however none
of those changes are yet tracked on our branch (no add or commit calls have 
been made by us yet). Then we made some more changes to the files on this branch by copying a file into the Code-Review/ directory and making changes to that. 

So how are we going to get eventually push only the changes to files that we changed to our repo? Simple, none of the changes that were made by other users
have been added or committed to this branch. So to add our code review we just need to make sure that this file is the only file that we add and commit. 

```bash
## add and commit only changes to your code-review file to your code-review branch
git add Code-Review/<your-username>.ipynb
git commit -m "completed by code review"
```

When you commit a message will popup saying that there are many untracked files
in your repo. That is fine, we don't care about these other files. We only 
wanted to commit changes to the one file in Code-Review/ that we changed ourselves. Now that we have made a commit on our new branch, 
we could either push this branch to GitHub if we think we have some reason to keep it long term, or, we can merge it back into our master branch. We'll do the latter. 

```bash
## switch to your master branch and merge in changes from the code-review branch
git checkout master
git merge code-review master
```

Now your master branch has inherited the commits that you made on the 
code-review branch. It is like you made the add and commit call on the 
master branch itself, except none of those other untracked files on the 
code-review branch are present on your master branch. You kept it nice and 
clearn. To finish, push these commits back to your GitHub 
origin remote and then on GitHub make a pull-request to the course remote. 

```bash
git push origin master
```

Don't forget to make your pull request on GitHub.



### Code Review Table

| Student | Assigned repo to review |
| --------| ------------------------|
| apf2139 | meairey |
| anikap22 | 	jessicahoch |
|  aprocton	| juliazeh |
| chloehacker |	apf2139
| cs3425  |	kuratanp
| drs22Col |Wenyi909 | 
| izrubin | mvanack | 
| jessicahoch | izrubin | 
| juliazeh | drs22Col | 
| kuratanp | nehasavant | 
| lucasrocmoreira | anikap22 | 
| meairey | lucasrocmoreira | 
| mistergroot | chloehacker | 
| mvanack | vjjan91 | 
| nehasavant | mistergroot | 
| vjjan91 | cs3425 | 
| Wenyi909 | aprocton |

