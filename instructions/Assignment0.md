# Assignment 0: Collaboration Policy & Git
### Due: Friday Sep 15, 11:59:59pm

## Introduction

This assignment is mainly intended to emphasize the importance of the _no plagiarism_ policy for collaboration. In the meantime, this assignment will help you get familiar with Github, which you will use to submit your assignments throughout this class. In particular, you will know how to clone the remote assignment repository to your local machine, how to work on your assignments locally, and how to submit them to the remote repository. Please note that we will grade your assignments *only* at the remote repository. 

## Part I: Zero Tolerance on Plagiarism
### Reading
 SEAS has a short page on academic integrity, avaiable at [Academic Integrity](http://gradengineering.columbia.edu/academic-integrity-0).

 Additionally, Columbia college has a list of behaviors that constitute [Academic Dishonesty](https://www.college.columbia.edu/academics/academicdishonesty).

 Carefully read those resources above. Please note that this class requires closely obeying the policy on academic integrity, and has zero tolerance on plagiarism for all assignments. For programming assignments, in particular, you must write all the code you hand in, except for code that we give you as part of the assignments. You are not allowed to look at anyone else's solution (including solutions on the Internet, if there are any), and you are not allowed to look at code from previous years. You may discuss the assignments with other students, but you may not write pseudocode together, look at or copy each other's code. Please do not publish your code or make it available to future students -- for example, please do not make your code visible on Github. 

 For each programming assignment, we will use software to check for plagiarized code.

 Remember: by "zero tolerance" we mean that the *minimum* punishment for plagiarism on any assignment is an "F" for this class.

### Agreeing to Course Policies

 After you have read the above resources, please "sign" `collaboration_policy.txt` located in your assignments root directory with your name, UNI, and the date.

## Part II: Using Git

 All programming assignments, including Assignment 0, require Git for submission. You'll fetch the initial assignment software with [Git](http://git.or.cz/) (a version control system). To learn more about Git, take a look at the [Atlassian's Git Tutorial](https://www.atlassian.com/git/tutorials/what-is-version-control).  We will generally provide you with the necessary commands to submit each assignment.  However, you should read this tutorial to understand how the system works, especially if this is your first time using Git.

 We are using Github for distributing and collecting your assignments. You should have received an email with instructions to set up your repository. If you have not yet received this, please email your TA. You will need to develop in a \*nix environment, i.e., Linux or OS X. To install the files in your development environment you need to _clone_ your private repository. Your Github page will have a link (click the green button titled "clone or download").  For simplicity, we assume that you will be working in your $HOME directory; of course, this is optional.

```bash
$ cd ~
$ git clone https://github.com/Columbia-COMS-4113-Fall-17/assignments-myusername.git 4113
$ ls 4113
collaboration_policy.txt
readme.md
src
```

Now , you will have all of the code and instructions needed to complete all of the assignments.  This is by design.  These assignments are challenging and time-consuming.  We recommend you start each assignment as early as possible.

At any point you can checkpoint your progress by "committing" you changes.  The `-a` flag allows you to bypass the `git add` command.  See the documentation above if your are interested in how staging and committing works.

```bash
$ git commit -am 'partial solution to assignment 0'
```

You should do this early and often!  You can _push_ your changes to Github after you commit with:

```bash
$ git push origin master
```

Please let us know that you've gotten this far in the assignment by pushing a _tag_ to Github. 

```bash
$ git tag -a -m "i got git and cloned the assignments" gotgit
$ git push origin gotgit
```

Tags are used by developers for version control; think of them as "super commits" that make it easy to find important checkpoints in your code.  We will be using tags for marking submission-ready code for future assignments. You should also be committing and pushing your progress regularly.

### Part 3: Submitting Assignment 0

 Now you need to submit your assignment 0, which confirms that you have read the collaboration policy and will comply. If you did not do so earlier, be sure to open the "collaboration\_policy.txt" file, and sign it by typing your name, UNI, and date. Then, commit your change and push it to the remote repository by doing the following:

```bash
$ git commit -am "[you fill me in]"
$ git tag -a -m "i finished assignment 0" a0handin
$ git push origin master
$ git push origin a0handin
```
