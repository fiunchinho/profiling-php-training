# Profiling PHP Applications
## Profiling
When we want to optimize the code running in our backend server we can use tools to analyze the performance of our application. These tools are called **profilers**.
A profiler instruments the code you want to measure and generates data about its runtime behavior. It does so by instrumenting the code. Data collected by profilers could include:

- The memory consumption;
- The number of times functions are called;
- The duration of functions' execution;
- The number of SQL queries;
- and much more.

Be warned that code instrumentation adds some overhead to the runtime execution. As such, it impacts time measurements. In other words, measuring time takes time! This is why good profiling tools must have minimal (or at least constant) overhead to avoid skewing results too much.

## Terminology
### Wall Clock Time
The Wall Clock Time, or Wall Time, for a function call is the measure of the real time it took for PHP to execute its code: the difference between the time at which PHP entered the function and the time at which PHP left the function.
Wall time is usually split in two main parts:
- **CPU Time**: The CPU time is the amount of time the CPU was used for processing instructions.
- **I/O Time**: The I/O time is the time the CPU waited for input/output (I/O) operations.

We can divide I/O time into two parts:
- **Network activity**: that includes calls to databases like MySQL, PostreSQL, or MongoDB; HTTP calls to web services and APIs; calls to cache systems like Redis and Memcached; communications with services like queues, email daemons, remote filesystems; etc.
- **Disk activity**: that occurs when a program reads files from the filesystem, including when PHP loads a script or class file.

Keep in mind that the I/O time is almost never 0 as it includes some non-significant activities (like memory access).

In summary:

> Wall Time = CPU Time + I/O Time
>
> I/O Time = Network Time + Disk Time

When measuring the time taken for a function call, we have to differentiate:
- **Inclusive time**: time or percentage of time spent in a given method or function. The inclusive time allows you to find the critical path of an application.
- **Exclusive time**: The exclusive time for a function call is the time spent in the function itself, excluding time spent in child calls. The exclusive time allows to find the function calls to optimize first. It tells you which function calls consumed most of the resources by themselves.

## Profiling with [Blackfire.io](https://blackfire.io)
The [Blackfire](https://blackfire.io) stack is made of these components:

- **Probe**: a PHP extension that instruments your code to gather performance profiles;
- **Agent**: a server-side daemon that receives, aggregates and forwards profiles from the Probe to blackfire.io;
- **Client**: tool to enable the profiling. You can use the Companion, web browser extension; or the command line tool like Companion packaged with the Agent;
- **[Blackfire Website](https://blackfire.io)**: used to visualize the profiles.


## Getting started with [Blackfire.io](https://blackfire.io)
- [Your First Profile](https://blackfire.io/docs/24-days/04-first-profile)
- [Validating Performance Optimizations](https://blackfire.io/docs/24-days/05-validating-performance-optimizations)
- [Profiling POST requests from the CLI](https://blackfire.io/docs/24-days/08-profiling-all-the-things)
- [Installing Blackfire](https://blackfire.io/docs/up-and-running/installation)

## Deployment of this repository
If you want to deploy the content of this repository to an EC2 instance follow these simple steps.
Go to the AWS Console and start a new EC2 micro instance. Choose the default VPC. To be able to later access the machine, create a new pair of SSH keys or choose an already existing pair. Wait for it to be available.

While this happens, install on your Vagrant [Ansible](https://docs.ansible.com/ansible/) with the [Ansistrano](https://github.com/ansistrano/deploy) and [Blackfire](https://galaxy.ansible.com/detail#/role/3201) roles

```bash
$ sudo easy_install pip
$ sudo pip install ansible
$ ansible-galaxy install carlosbuenosvinos.ansistrano-deploy carlosbuenosvinos.ansistrano-rollback geerlingguy.php AbdoulNdiaye.Blackfire
```

The EC2 instance should be available now. Go to the AWS Console and then to Security Groups. Choose the security group that you are using for the instance and allow all traffic from the internet to the machine. This is insecure but it's just for this training.
Try sshing into the machine selecting your pem key. Choose the key that you selected while creating the instance. Remember that for Ubuntu instances, the SSH user is `ubuntu`.

```bash
$ ssh -i $HOME/.ssh/personal.pem ubuntu@52.123.456.78
```

Edit the `deploy.yml` and `rollback` files, mainly `blackfire_server_id` and `blackfire_server_token`. You can find these values in your [Blackfire profile page](https://blackfire.io/account).
Don't forget to select the `ansistrano_deploy_to` variable. This is the path where your application will be deployed.

Finally, execute Ansible to deploy

```bash
$ ansible-playbook --private-key $HOME/.ssh/personal.pem -u ubuntu -i 52.48.238.88, deploy.yml
```

Try to deploy several times and watch the `releases` folder grow.