<!-- Start with workshop_00.md -->

# I want to have a _command line utility_ installed
# So that I can _deploy apps into cloud.gov_

^ We have our login, now how do we run stuff? With a CLI

---

# Why the `cf` CLI?

The Cloud Foundry command-line interface (CLI) is a multiplatform binary written in `Go` to interact with the _CF API_.  The CLI provides:

* Automation
* Collaboration
* Corroboration

^ You can put commands into files, with variables, and then run them.<br> You can share code by viewing text files, or even copy/paste, in ways you can't with GUIs (nb: skip Snover anecdote). <br> And with version control you can corroborate that the change you mean to make is the change expressed by the code. <br> With the dashboard we provide overview tools and roles/permissions since those may be needed by project managers how are not using the CLI day-by-day


---

# Lab 2: Install cloudfoundry tools and login to cloud.gov

---
# 2.1 Select and install the appropriate installer for your computer: 

Go to [https://github.com/cloudfoundry/cli/releases](https://github.com/cloudfoundry/cli/releases) and select an `Installer` for your system. Download and go through the installation steps.

On Macs, with Homebrew, you can use: 

```sh
     brew cask install cloudfoundry-cli
```

On Workspaces, `cf` CLI is already installed.

^ I have not shown the Installer steps as they're different for different systems, and should be familiar to you at this point.

---

# 2.1 continued...

After the installer has finished, run the command:

```sh
> cf
``` 

and you should see a list of command options.

^ When you have CF installed and working as on the next slide, we'll continue with authenticating your CLI to your _cloud.gov_ account.

---

# Check your work 2.1

You should see output similar the to the following:

```sh
PS /Users/peterburkholder> cf
cf version 6.26.0+9c9a261fd.2017-04-06, Cloud Foundry command line tool
Usage: cf [global options] command [arguments...] [command options]

Before getting started:
  config    login,l      target,t
  help,h    logout,lo

... [snip] ...
```

^ Instructor: wait here until most partipants have CF installed.

---

# 2.2 Login to cloud.gov with the _cf_ CLI

You'll enter the command below, and you'll be directed to an _authentication URL_. 

```powershell
cf login --sso -a https://api.fr.cloud.gov
```

Confirm you're logged in by seeing the _orgs_ you belong to:

```powershell
cf orgs
```

---

# Check your work 2.2

```
> cf login --sso -a https://api.fr.cloud.gov
API endpoint: https://api.fr.cloud.gov

One Time Code ( Get one at https://login.fr.cloud.gov/passcode )>
```

Visit the URL [https://login.fr.cloud.gov/passcode](https://login.fr.cloud.gov/passcode), complete the login to _cloud.gov_, and you'll get a one-time passcode. Copy/paste the passcode back into the CLI, as show [in this 30s video](TKTK).

---

# Check your work 2.2, continued

```powershell
> cf orgs
Getting orgs as peter.burkholder@cao.gov...

name
sandbox-cao
```

---

# Further exploration

Once you have `cf orgs` working, try the following:

* `cf logoug`: Auto-suggest on _misspellings_
* `cf help`: Explore other commands
* `cf curl "/v2/spaces"`: Peek into the API internals[^1]

[^1]: This is a peek at the guru-level view of Cloud Foundry. You'll not need this anytime soon.

^Instructor: Wait here until most folks have `cf orgs` working

---

# I want my website to be _accessible at a public URL_
# So that the _American people_ can read it

---

# Lab 3: Download _workshop labs_ and deploy a _static website_ to _yourname_.app.cloud.gov

Our simplest example. We'll get our _lab materials_, then use `cf push` to send the files to _cloud.gov_.  Cloud.gov will package the site and start to serve it.

^ We have working environment, a cloud.gov account, a working `cf` connection to the cloud.gov account. Now we need to materials for the rest of our labs. Then we can release a website.

---

# 3.1: Download labs 

Mac/Linux shell:

```shell
cd $HOME
curl -Lo cgw.zip http://bit.ly/cgw-zip
unzip cgw.zip
cd cg-workshop-master
```

Windows Powershell:

```powershell
cd $HOME
iwr -o cgw.zip https://bit.ly/cgw-zip
7z x cgz.zip   # If no 7zip, use File Explorer to unpack
cd cg-workshop-master
```

^ Instructor: Run through the entire set of slides then wait for participants to catch up

---

# Check your work 3.1

Run `ls`. You should see output similar to the following:

```powershell
PS D:\Users\cao.burkholder\cg-workshop-master> ls

    Directory: D:\Users\cao.burkholder\cg-workshop-master

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        9/25/2017   9:13 PM                admin
d-----        9/25/2017   9:13 PM                images
d-----        9/25/2017   9:13 PM                lab01-setup
d-----        9/25/2017   9:13 PM                lab03-site
------        9/25/2017   9:13 PM             47 .gitignore
------        9/25/2017   9:13 PM          13225 00-slides-cloudgov.md
```

---


# Lab 3.2: Deploy static website

Don't literally use _myfname-lname_ below. Use your own name like, _jane-doe_:

```
cf push -f lab03-site/manifest.yml myfname-lname
```

^ If you do use myfname-lname, you'll run into a `route conflict` with someone else trying to use that same name.`

---

[.build-lists: true]
# What happens when I _cf push_? (v1.0)


* Upload: Files are sent to CF for new app _myfname-lname_
  * `-f lab-03-site/manifest.yml` is a _deployment manifest_
* Staging: 
  * Artifact is created (droplet)
* Running: 
  * A _route_ is created to the app
  * Site starts on an web host
  * Site serves web requests 

---

![fit](images/app_push_flow_diagram_diego.png)

^ You run a `cf` command. Terms: "Binary data store" "database"<br>Applications are run in 'containers' hosted in 'Cells'. First a staging cell builds your application and all the dependencies, and stores in the binary store. Then 'Running' cell can pick it up and run one or more copies of it.

---

# Check your work 3.2

The `cf push` results should resemble:

```
$ cf push -f lab03-site/manifest.yml peter-burkholder
Creating app peter-burkholder in org s-cao / space p.burk...
OK
Uploading peter-burkholder...
... [snip]...
rrequested state: started
instances: 1/1
usage: 16M x 1 instances
urls: peter-burkholder.app.cloud.gov
last uploaded: Tue Sep 26 14:27:12 UTC 2017
stack: cflinuxfs2
buildpack: staticfile

     state     since                    cpu    memory        disk          details
#0   running   2017-09-26 10:27:29 AM   0.0%   3.9M of 16M   6.2M of 32M
```

[.footer: continued...]

---

# Check your work 3.2, continued

Now try accessing your site:

```powershell
# powershell
iwr https://myfname-lname.app.cloud.gov
# or shell
curl https://myfname-lname.app.cloud.gov
# or mac
open https://myfname-lname.app.cloud.gov
```
![right,fit](images/hello-cloud-gov.png)


E.g.: 

```powershell
> curl https://peterburkholder.app.cloud.gov
<body>
  <h1>Hello from cloud.gov</h1>
</body/
```

---

# Further exploration

When you can access your site, try the following:

* Try HTTP, e.g., `http://myfname-lname.app.cloud.gov`
  * Does it work? Is it secured?
* `cf app myfname-lname`
  * What info do you get about your app
* `cf push -f lab03-site/manifest.yml myfname-lname --random-route`
  * What URL do you use now?

^ Instructor: Wait here for most participants to reach their static page.

---

# BREAK 

We'll break here so folks can catch up with:

* workstation setup
* account creation - can you login to:   [https://dashboard.fr.cloud.gov](https://dashboard.fr.cloud.gov)?
* CLI install - can you run:   `   cf   `   ?
* labs download - can you:   ```    cd $HOME/cg-workshop-master   ```   ?

You can skip doing _cf push_ as we'll pick up from there in the next section.

---

# I want to run a _dynamic webapp_
# So that users can _interact_ with us

---

# Lab 4: Sinatra Application

We'll use _cf push_ again, but this time to _stage_ and run a dynamic web applicaton. We'll see how to use the _manifest.yml_ to set deployment options.

The manifest provides application _metadata_ to CloudFoundry. We use it for non-default settings so we don't have to always specify on the command line, and we can bundle with the application.

^ As with static site, let's review the slides, then we'll pause for everyone to run the commands<br> Sinatra is a _ruby_ web framework. The deployment process is the same for any supported Cloud Foundry language.

---

[.build-lists: true]

# What happens when I _cf push_? (v2.0)

* Upload: **App** files are sent to CF for new app _myfname-lname_
* Staging: 
  * **Executable** artifact is created (droplet)
  * **All build dependencies are bundled into _droplet_**
* Running: 
  * A _route_ is created to the **app** ~~site~~
  * ~~Site~~ **App** starts on an ~~web~~ **app** host
  * ~~Site~~ **App** serves web requests **(if it binds to TCP port)**

^ This last point -- we can also have _worker_ applications that's don't directly server traffic. Will Slack will mention those with Federalist.  Let's revisit the push command and update for an application. There's extra work in the _staging_ phase, where _buildpacks_ come into play.

---

# Buildpacks create a runnable artifact called a droplet

App Files + Runtime Dependencies = App Artifact (droplet)

# Apps are started on specialized VMs called cells

If it’s a web process, it binds to a TCP port
Instances are distributed across multiple cells
Router distributes traffic across instances

^ If you look at the labs04-app/Gemfile you'll see a lot of dependencies that the buildpack handles.

---

# Where does the app run?

```
                           CONSUMER

                              |

                            ROUTER

                            /    \
                           /      \
                          /        \
                         /          \

                      CELL 1       CELL 2

                        |            |
      APP A INSTANCE 1 -|            |- APP A INSTANCE 2
      APP B INSTANCE 2 -|            |- APP B INSTANCE 1
```

---

# Lab 4.1:  Review the _deployment manifest_

```powershell
more lab04-app/manifest.yml
```

How much memory/disk are we saving compared to defaults of 512Mb RAM and 1024Mb disk quota?

---

# Check your work 4.1

The manifest should contain:

```yaml
---
applications:
- name: cglab
  memory: 64m
  disk-quota: 128m
  random-route: true
#  buildpack: ruby_buildpack
```

All of us will have an app, _cglab_, but we can't all have it _routed_ to 
https://cglab.app.cloud.gov.

_random-route_ will append random words to the URL.

^ We use 1/8th the resource allocation.

---

# Lab 4.2 Push the application

```powershell
cf push -f lab04-app/manifest.yml
```

^ There's no need for `name` since that's in the manifest.

---

# Check your work 4.2

The cf push results should resemble those below. Note all the buildpacks (and use of buildpack detection)

```powershell, [.highlight: 9]
$ cf push
Using manifest file cg-workshop/04_webapp/manifest.yml
... [snip] ...
Starting app cglab in org sandbox-cao / space peter.burkholder as peter.burkholder@cao.gov...
Downloading nodejs_buildpack...
Downloading php_buildpack...
Downloading dotnet_core_buildpack...
Downloading java_buildpack...
Downloaded ruby_buildpack (81.6K)
... [snip] ...
```

[.footer: continued....]

---

# check your work, continued

The cf push final output should resemble below. Note the highlighted `urls`. Since we use `random-route` the URL here is [https:/cglab-confessable-pardner.app.cloud.gov](https://cglab-confessable-pardner.app.cloud.gov)

```powershell, [.highlight: 4]
...
instances: 1/1
usage: 64M x 1 instances
urls: cglab-confessable-pardner.app.cloud.gov
last uploaded: Thu Sep 21 01:48:46 UTC 2017
stack: cflinuxfs2
buildpack: ruby

     state     since                    cpu    memory      disk      details
#0   running   2017-09-20 09:49:19 PM   0.0%   0 of 64M   0 of 128M 
```

[.footer: continued....]

--- 

# check your work, continued

Interact with the webpage at <br>
https://cglab-RANDOM-WORDS.app.cloud.gov <br>
e.g., <br>
[https:/cglab-confessable-pardner.app.cloud.gov](https://cglab-confessable-pardner.app.cloud.gov)

![right,fit](lab04-app/images/webapp.png)


---

# Lab 4.3 Review the app status and health.

Run:

```
cf app cglab
```

How much memory and disk is it using?

---

# Check your work 4.3

The _cf app_ output should resemble:

```powershell, [.highlight: 6, 13]
Showing health and status for app cglab in org sandbox-cao 

name:              cglab
requested state:   started
instances:         1/1
usage:             64M x 1 instances
routes:            cglab-confessable-pardner.app.cloud.gov
last uploaded:     Wed 20 Sep 22:06:14 EDT 2017
stack:             cflinuxfs2
buildpack:         ruby

     state     since                  cpu    memory          disk          details
#0   running   2017-09-21T02:06:44Z   0.0%   19.2M of 64M   80.8M of 128M 
```

---

# Further exploration

Once you've visited your app and viewed `cf app cgapp`, try the following:

* Run `cf buildpacks`. What languages are available by default?
* Uncomment the `manifest.yml` line with `buildpack`, then run `cf push` and check status with `cf app cglab`. What's changed in staging or application status?
* Does the updated manifest change release time? Try `time cf push` (shell) or `Measure-Command {cf push}` (powershell)

^ Instructor: Pause here

---

# I want to store data in a service
# So that it is persistent and shared

---


# Lab 5. I can share persistent data between app instances

First we'll see the _services_ available to us, then use _create-service_ 
to provision a simple Redis data store. We'll then _bind_ that service to 
our application.  Our application we'll use its _environment variables_ to determine
the connection information


---

# Lab 5.1 Review the available _services_

Run `cf marketplace` and look for the _redis_ services. Examine the details with `cf marketplace -s redis32`. 

---

# Check your work 5.1

```
$ cf marketplace
Getting services from marketplace in org sandbox-cao / space peter.burkholder as peter.burkholder@cao.gov...
OK

service                       plans                         description
aws-rds                       shared-psql, medium-psql* ... Persistent DBs using Amazon RDS
...
redis28                       standard         An open source in-memory data structure store.
redis32                       micro, standard-ha, standard       An open source in-memory database.
```

---

# Check your work 5.1, continued

```
$ cf marketplace -s redis32
Getting service plan information for service redis32 as peter.burkholder@cao.gov...
OK

service plan   description                                             free or paid
micro          Redis 3.2, persistent storage, 32m.                     free
standard-ha    Redis 3.2 with Redis Sentinel and persistent storage.   free
standard       Redis 3.2 with persistent storage.                      free
```

---

# Lab 5.2: Create a Redis service with _create-service_

See the command help with `cf create-service -h`. Note the format is:
`cf create-service redis32  PLAN  NAME`

Then issue the command: 
`cf create-service redis32  micro  cglab-redis`

Wait one minute, then check your service with:
`cf service cglab-redis`

---

# Check your work 5.2

```
$ cf create-service redis32  micro cglab-redis
Creating service instance cglab-redis in org sandbox-cao 
OK

Create in progress. Use 'cf services to check


$ cf service cglab-redis

Service instance: cglab-redis
Service: redis32
Bound apps:
... [snip] ...
Started: 2017-09-21T14:40:57Z
Updated: 2017-09-21T14:42:01Z
```

---

# Lab 5.3 Associate service and app with _bind-service_

When `cf service cglab-redis` status is healthy, get
current app `environment` with: 
`cf env cglab`.

Then bind service with: 
`cf bind-service cglab cglab-redis`.

Compare new results of 
`cf env cglab`

---

# Check your work 5.3

```
$ cf env cglab
Getting env variables for app cglab in org sandbox-cao 
OK

System-Provided:


{
 "VCAP_APPLICATION": {
... 

$ cf bind-service cglab cglab-redis
Binding service cglab-redis to app cglab in org sandbox-cao 
OK
TIP: Use 'cf restage cglab' to ensure your changes take effect
```
---

# Check your work 5.3, continued
 
```
$ cf env cglab
Getting env variables for app cglab in org sandbox-cao
OK

System-Provided:
{
 "VCAP_SERVICES": {
  "redis32": [
...
```

---

# Lab 5.4 Push the new version of our app

Change directory to 
`cg-workshop/07-shared-state`

and run 
`cf push`

Check status with 
`cf app cglab` 

and visit your app at the random-route URL. Refresh page multiple times.

---

# Check your work 5.4

```
$ cf push
Using manifest file .../cg-workshop/07-shared-state/manifest.yml

Updating app cglab in org sandbox-cao 
OK
...
$ cf app cglab
Showing health and status for app cglab in org sandbox-cao 

name:              cglab
requested state:   started ...
routes:            cglab-gastrocnemian-calefaction.app.cloud.gov
...

     state     since                  cpu    memory         disk         
#0   running   2017-09-21T15:17:06Z   0.0%   19.8M of 64M   83.9M of 256M
```
---

# Check your work 5.4, continued

![fit](lab07-state/images/running.png)


---

# Lab 5.5 Scaling

Create an additional app instance with 
`cf scale cglab -i 2`

Refresh webapp page multiple times

Then scale back to one with 
`cf scale cglab -i 1`

---

# Check your work 5.5 (TK)

--- 

# Review: Where does the app run?

```
                           CONSUMER

                              |

                            ROUTER

                            /    \
                           /      \
                          /        \
                         /          \

                      CELL 1       CELL 2

                        |            |
      APP A INSTANCE 1 -|            |- APP A INSTANCE 2
      APP B INSTANCE 2 -|            |- APP B INSTANCE 1
```

---

# Further exploration

* Go to your app's URL + '/env": http://cglab....app.cloud.gov/env
* Use `cf set-env` to add new variables
  * Hint: You'll need `cf restage` for your app to pick them up.

---

# I want to know what my CF app is doing
# So that I can debug it

---

#  6. I can investigate my apps to determine the cause of errors

---


* Maintainance, logging and debugging
  * Restage to pick up new buildpacks: 
  `cf restage cglab`
  * Platform availability: 
  `cf events cglab`
  * Logs:
  `cf logs cglab`
  `cf logs --recent cglab`
  * Live instance degbugging: 
  `cf ssh cglab`
  * [https://cloudgov.statuspage.io](https://cloudgov.statuspage.io)
  * [https://logs.fr.cloud.gov](https://logs.fr.cloud.gov)

---

1. CircleCI  - Demo only! or optional for GitHub users 
  * Accomplished:
    - Ensuring continuous integration testing
    - Ensure consistent environments

---

1. Access control -Demo only, skip if short on time.
  * `cf orgs`
  * `cf spaces`
  * Discussion / Demo only
  * Accomplished: 
    * Inbound access control w/ oauth - 
    * Egress to external providers by fixed IP

---

# YAGNI

1. Blue-green releases
  * Autopilot
1. Optional: Worker apps
  * See Aidan's notes for a good write-up
1. Development tips
  * `cf local`

---

# Closing: The 12-factor App

---

# Parking lot

- health checks