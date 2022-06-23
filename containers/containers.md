# <font color=black> Container primitives </font>

## <font color=Magenta> :zero: Initial setup

<font color=black> Because we need to use Linux primitives, you will need access to a Linux OS. Some of you might have one, others might not. Well, you can even run an Ubuntu VM on your Windows machine, if you follow along this [article](https://phoenixnap.com/kb/hyper-v-ubuntu)!

## <font color=green> :one: Namespaces

### :page_with_curl: Theory

<font color=black> Namespaces have been added to the Linux kernel in 2002. Container support, however, was added to the Linux kernel in 2013. This is what made namespaces really useful and brought them to the masses.

Here's a definition from [Wikipedia](https://en.wikipedia.org/wiki/Linux_namespaces):

“Namespaces are a feature of the Linux kernel that partitions kernel resources such that one set of processes sees one set of resources while another set of processes sees a different set of resources.”

### :computer: Practice

##### :white_circle: Creating our own namespace

Open two terminals and follow along:
```
T1: ps -ef 
T2: ps -ef
```
You should see the same output in both terminals

```
T1: unshare --user --pid --map-root-user --mount-proc --fork bash
```
This will create a new namespace in which `bash` will be your main process `PID 1`

##### :white_circle: Test it out

```
T1: ps -ef 
T2: ps -ef
```

Now in terminal `T1` you should see only 2 processes: `bash` and `ps -ef`, while in `T2` you should see all of the processes running inside of your OS.

This happens because now, in terminal `T1` you have created and entered a new namespace, basically a new encapsulated tree of processes which are unable to see outside of its own process tree. All processes we will open in `T1` will become children of `PID 1 = bash` and won't be able to see any other processes running on the same OS, where `PID 1 = <OS init command>`.

##### :white_circle: Cleanup

All you need to do is kill the process that started it all, so just find the PID of the `unshare --user --pid --map-root-user --mount-proc --fork bash`command by using the `ps -ef`output:

```
kill <PID of unshare>
```

## <font color=green> :two: cGroups

### :page_with_curl: Theory

<font color=black>A control group (cgroup) is a Linux kernel feature that limits, accounts for, and isolates the resource usage (CPU, memory, disk I/O, network, and so on) of a collection of processes.

Cgroups provide the following features:

:bulb:Resource limits – You can configure a cgroup to limit how much of a particular resource (memory or CPU, for example) a process can use.
:bulb:Prioritization – You can control how much of a resource (CPU, disk, or network) a process can use compared to processes in another cgroup when there is resource contention.
:bulb:Accounting – Resource limits are monitored and reported at the cgroup level.
:bulb:Control – You can change the status (frozen, stopped, or restarted) of all processes in a cgroup with a single command.

### :computer: Practice
##### :white_circle: Create a cGroup

Open two terminals

```
T1: sudo su
T1: mkdir -p /sys/fs/cgroup/memory/shikki
T1: echo 30000000 > /sys/fs/cgroup/memory/shikki/memory.limit_in_bytes
```

##### :white_circle: Assign process to cGroup

Create your testing script
```
T1: echo "echo 'cGroup testing tool started...' && sleep 3333" > test.sh
T1: chmod +x ./test.sh
T1: ./test.sh &
```
Assign the process to the cGroup
```
T2: sudo su
T2: echo [script PID] > /sys/fs/cgroup/memory/shikki/cgroup.procs
T2: ps -o cgroup [script PID]
```
You should now see in the output that the memory consumption policy is being enforced by the `shikki` cgroup.

##### :white_circle: Cleanup

Just interrupt the running process & delete the created cGroup:

```
T1: ctrl + C           # to stop test.sh
T1: sudo rm -rf /sys/fs/cgroup/memory/shikki
```

If cGroup still persists, restart your VM and it should no longer be there

## <font color=green> :three: chRoot

### :page_with_curl: Theory
<font color=black>Chroot does one thing—run a command with a different root directory. The command being run has no idea that anything outside of its jail exists, as it doesn’t have any links to it, and as far as it’s aware, is running on the root filesystem anyway. There’s nothing above root, so the command can’t access anything else.

### :computer: Practice
##### :white_circle: Send a process to jail

Open one terminal and create the jail folder

```
sudo su        # switch to root user
mkdir /jail
```
We now want to run `bash` inside the jailed folder, so let's put a bash in there.
First, find `bash` dependencies needed to run properly
```
ldd $(which bash)
```
Copy dependencies inside jail
```
cp /bin /jail/bin
cp /lib /jail/lib
cp /lib64 /jail/lib64
```
Copy bash executable inside jail
```
cp -a /bin/bash /jail/bin/bash
```
Open bash inside jail
```
chroot /jail bash
```
Once in jail, use `ctrl + D` to leave (way easier than in Monopoly)

##### :white_circle: Cleanup
Remove the `jail` folder
```
rm -rf /jail
```

### :bow: Credits 
[Nginx blog](https://www.nginx.com/blog/what-are-namespaces-cgroups-how-do-they-work/)
[Howtogeek](https://www.howtogeek.com/devops/what-is-chroot-on-linux-and-how-do-you-use-it/)