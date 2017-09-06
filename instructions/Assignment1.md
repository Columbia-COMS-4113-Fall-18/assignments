# Assignment 1: MapReduce
### Due: Friday Sep 22, 11:59:59pm

### Introduction

In this assignment you'll build a MapReduce library as a way to learn the Go
programming language and as a way to learn about fault tolerance in
distributed systems. In the first part you will write a simple
MapReduce program. In the second part you will write a Master that
hands out jobs to workers, and handles failures of workers. The
interface to the library and the approach to fault tolerance is
similar to the one described in the original
[MapReduce paper](http://research.google.com/archive/mapreduce-osdi04.pdf).

### Collaboration Policy

Please refer to Assignment 0.

### Software

You'll implement this assignment (and all the assignments) in [Go 1.2 or
later](http://www.golang.org/). The Go web site contains lots of
tutorial information which you may want to look at. We supply you with
a non-distributed MapReduce implementation, and a partial
implementation of a distributed implementation (just the boring bits).

It's your responsibility to install Go in your development
environment. We recommend using your distribution's package manager. 

On OS X with [homebrew](http://brew.sh/):
```bash
$ brew install go
```

On Ubuntu/Debian:
```bash
$ sudo apt-get install golang
```

On Arch:
```bash
$ sudo pacman -S go
```


### Getting started

 There is an input file <tt>kjv12.txt</tt> in src/main, which was
downloaded from [here](https://web.archive.org/web/20130530223318/http://patriot.net/~bmcgin/kjv12.txt).
Compile the initial software we provide you with and run it with the downloaded input
file:

```bash
$ export GOPATH=$HOME/4113
$ cd ~/4113/src/main
$ go run wc.go master kjv12.txt sequential
# command-line-arguments
./wc.go:11: missing return at end of function
./wc.go:15: missing return at end of function
```

The compiler produces two errors, because the implementation of the
<tt>Map</tt> and <tt>Reduce</tt> functions is incomplete.

### Part I: Word count

Modify <tt>Map</tt> and <tt>Reduce</tt> so that <tt>wc.go</tt> reports the
number of occurrences of each word in alphabetical order.
```bash
$ go run wc.go master kjv12.txt sequential
Split kjv12.txt
Split read 4834757
DoMap: read split mrtmp.kjv12.txt-0 966954
DoMap: read split mrtmp.kjv12.txt-1 966953
DoMap: read split mrtmp.kjv12.txt-2 966951
DoMap: read split mrtmp.kjv12.txt-3 966955
DoMap: read split mrtmp.kjv12.txt-4 966944
DoReduce: read mrtmp.kjv12.txt-0-0
DoReduce: read mrtmp.kjv12.txt-1-0
DoReduce: read mrtmp.kjv12.txt-2-0
DoReduce: read mrtmp.kjv12.txt-3-0
DoReduce: read mrtmp.kjv12.txt-4-0
DoReduce: read mrtmp.kjv12.txt-0-1
DoReduce: read mrtmp.kjv12.txt-1-1
DoReduce: read mrtmp.kjv12.txt-2-1
DoReduce: read mrtmp.kjv12.txt-3-1
DoReduce: read mrtmp.kjv12.txt-4-1
DoReduce: read mrtmp.kjv12.txt-0-2
DoReduce: read mrtmp.kjv12.txt-1-2
DoReduce: read mrtmp.kjv12.txt-2-2
DoReduce: read mrtmp.kjv12.txt-3-2
DoReduce: read mrtmp.kjv12.txt-4-2
Merge phaseMerge: read mrtmp.kjv12.txt-res-0
Merge: read mrtmp.kjv12.txt-res-1
Merge: read mrtmp.kjv12.txt-res-2
```

The output will be in the file "mrtmp.kjv12.txt". Your implementation is
correct if the following command produces the following top 10 words:
```bash
$ sort -n -k2 mrtmp.kjv12.txt | tail -10
unto: 8940
he: 9666
shall: 9760
in: 12334
that: 12577
And: 12846
to: 13384
of: 34434
and: 38850
the: 62075
```

To make testing easy for you, run:
```bash
$ ./test-wc.sh
```
and it will report if your solution is correct or not.

Before you start coding reread Section 2 of the [MapReduce
paper](http://research.google.com/archive/mapreduce-osdi04.pdf) and our code for MapReduce, which is in <tt>mapreduce.go</tt> in
package <tt>mapreduce</tt>. In particular, you want to read the code of the
function <tt>RunSingle</tt> and the functions it calls. This will help you 
understand what MapReduce does and learn Go by example.

Once you understand this code, implement <tt>Map</tt> and <tt>Reduce</tt> in
<tt>wc.go</tt>.


Hint: you can use
[<tt>strings.FieldsFunc</tt>](http://golang.org/pkg/strings/#FieldsFunc)
to split a string into components.

Hint: for the purposes of this exercise, you can consider a word to be
any contiguous sequence of letters, as determined by
[<tt>unicode.IsLetter</tt>](http://golang.org/pkg/unicode/#IsLetter).
A good read on what strings are in Go is the [Go Blog on strings](http://blog.golang.org/strings).

Hint: the strconv package (http://golang.org/pkg/strconv/) is handy to
convert strings to integers, etc.

You can remove the output file and all intermediate files with:
```bash
$ rm mrtmp.*
```

```bash
$ git commit -am "[you fill me in]"
$ git tag -a -m "i finished assignment 1 part 1" a1p1
$ git push origin master
$ git push origin a1p1
$
```

### Part II: Distributing MapReduce jobs

In this part you will design and implement a master who distributes
jobs to a set of workers. We give you the code for the RPC messages
(see <tt>common.go</tt> in the <tt>mapreduce</tt> package) and the code
for a worker (see <tt>worker.go</tt> in the <tt>mapreduce</tt> package).

Your job is to complete <tt>master.go</tt> in the <tt>mapreduce</tt>
package. In particular, the <tt>RunMaster()</tt> function in
<tt>master.go</tt> should return only when all of the map and reduce tasks
have been executed. This function will be invoked from the <tt>Run()</tt>
function in <tt>mapreduce.go</tt>.

The code in <tt>mapreduce.go</tt> already implements the
<tt>MapReduce.Register</tt> RPC function for you, and passes the new
worker's information to <tt>mr.registerChannel</tt>. You should process
new worker registrations by reading from this channel.

Information about the MapReduce job is in the <tt>MapReduce</tt> struct,
defined in <tt>mapreduce.go</tt>. Modify the <tt>MapReduce</tt> struct to
keep track of any additional state (e.g., the set of available workers),
and initialize this additional state in the <tt>InitMapReduce()</tt>
function. The master does not need to know which Map or Reduce functions
are being used for the job; the workers will take care of executing the
right code for Map or Reduce.

In Part II, you don't have to worry about the failures of the workers. You are
done with Part II when your implementation passes the first test set in
<tt>test_test.go</tt> in the <tt>mapreduce</tt> package.

<tt>test_test.go</tt> uses Go's unit testing. From now on all exercises
(including subsequent assignments) will use it, but you can always run the actual
programs from the <tt>main</tt> directory. You run unit tests in a package
directory as follows:

```bash
$ go test
```

The master should send RPCs to the workers in parallel so that the workers
can work on jobs concurrently. You will find the <tt>go</tt> statement useful
for this purpose, and so is the [Go RPC documentation](http://golang.org/pkg/net/rpc/).

The master may have to wait for a worker to finish before it can hand out
more jobs. You may find channels useful to synchronize the threads that are waiting
for reply with the master once the reply arrives. Channels are explained in the
document on [Concurrency in Go](http://golang.org/doc/effective_go.html#concurrency).

We've given you code that sends RPCs via "UNIX-domain sockets".
This means that RPCs only work between processes on the same machine.
It would be easy to convert the code to use TCP/IP-based
RPC instead, so that it would communicate between machines;
you'd have to change the first argument to calls to Listen() and Dial() to
"tcp" instead of "unix", and the second argument to a port number
like ":5100". You will need a shared distributed file system.

The easiest way to track down bugs is to insert log.Printf()
statements, collect the output in a file with <tt>go test &gt;
out</tt>, and then think about whether the output matches your
understanding of how your code should behave. The last step is most important.

Please let us know that you've gotten this far in the assignment, by
pushing a tag to github.

```bash
$ git commit -am "[you fill me in]"
$ git tag -a -m "i finished assignment 1 part 2" a1p2
$ git push origin master
$ git push origin a1p2
```

### Part III: Handling worker failures

In this part you will make the master handle worker failures. In
MapReduce handling failures of workers is relatively straightforward,
because the workers don't have persistent state. If the worker fails,
any RPCs that the master issued to that worker will fail (e.g., due to
a timeout). Thus, if the master's RPC to the worker fails, the master
should re-assign the job given to the failed worker to another worker.

An RPC failure doesn't necessarily mean that the worker failed; the worker
may just be unreachable but still computing. Thus, it may happen that two
workers receive the same job and compute it. However, because jobs are
idempotent, it doesn't matter if the same job is computed twice - both times it
will generate the same output. So, you don't have to do anything special for this
case. (Our tests never fail workers in the middle of job, so you don't even have
to worry about several workers writing to the same output file.)

You don't have to handle failures of the master; we will assume it
won't fail. Making the master fault tolerant is more difficult because
it keeps persistent state that must be replicated in order to make the master
fault tolerant. Keeping replicated state consistent in the presence of
failures is challenging. Much of the later assignments is devoted to this
challenge.

Your implementation must pass two remaining test cases in
<tt>test_test.go</tt>. The first case tests the failure of one
worker. The second test case tests handling of many failures of
workers. Periodically, the test cases start new workers that the
master can use to make progress, but these workers fail after
handling a few jobs.

### Handin procedure

You hand in your assignment exactly as you've been letting us know
your progress:

```bash
$ git commit -am "[you fill me in]"
$ git tag -a -m "i finished assignment 1" a1handin
$ git push origin master
$ git push origin a1handin
```

You should verify that you are able to see your final commit and your
a1handin tag on the Github page in your repository for this
assignment.

You will receive full credit if your software passes the
<tt>test_test.go</tt> tests when we run your software on our machines.
We will use the timestamp of your **last** a1handin tag for the
purpose of calculating late days, and we will only grade that version of the
code. (We'll also know if you backdate the tag, don't do that.)

### Questions

Please post questions on [Piazza](https://www.piazza.com/columbia/fall2017/comsw4113/home).
