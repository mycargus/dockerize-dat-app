# dockerize-dat-app
A brief, hands-on, high-level introduction to Docker.

This is *not* a substitute for Docker's [official tutorial](https://training.docker.com/self-paced-training). Rather, I'll attempt to briefly explain what Docker has to offer. Then we're going to dive right in to the command line and make some Docker magic.

### Why use Docker?

Have you ever run into unexpected errors while developing on your local machine? Problems like outdated dependencies, ruby library version confusion, or flat out jacked up stuff that leads to hours of scouring Stack Overflow and really weird looking old-school discussion threads in which the most valuable contributor is a self-proclaimed hacker with the alias "T0xic Vap0r"?

Yeah. Maintaining dependencies for numerous projects on a single machine can become a nightmare.

Or how about the classic software development conundrum, "It works on my machine!" :facepalm:

Docker aims to solve these problems, among others.

### What does Docker do?

Docker gives you the ability to replicate the exact same environment across all major operating systems (Windows, Mac, Linux). No more dependency nightmares or "It worked on my machine!" 

Now imagine you're a brand new QA Engineer at Google (or Instructure?). Your job is to QA _*everything*_. Imagine how long the technical onboarding would take!

What if you could do that in less than an hour? What if you could do it while getting lunch with your new team?

Docker makes this dream a reality!

I don't work for Docker, I promise.

### Where do I start?

Go ahead and read [this](https://docs.docker.com/mac/step_two/). I'll wait here.

You'll want to remember this terminology:
```
"A `container` is a stripped-to-basics version of a Linux operating system." (think: virtual machine)

"An `image` is software you load into a container."
```

At some point I highly recommend you work your way through Docker's extremely useful tutorials. For now, let's skip the under-the-hood principles and see Docker in action.

## Let's build something! (MacOSX)

#### Prerequisites
1. Ruby v2.3
2. [dinghy](https://github.com/codekitchen/dinghy) and its dependencies. Read dinghy's documentation for a full explanation if you like. Just know it will make your Dockerized life much easier.

If you haven't already followed dinghy's advice by adding the specified environment variables to your `.bashrc` file or equivalent, you need to do that now. 

You'll know everything is working when you execute these commands...
```sh 
$ dinghy up
$ dinghy status
```

... and you see this:
```sh
$ dinghy status
  VM: running
 NFS: running
FSEV: running
 DNS: running
HTTP: running

Your environment variables are already set correctly.
```

Onward to the fun stuff!

### Hello Docker World

###### Step 1: Create your App

For this example we'll use Sinatra. No need for a database.

```sh 
$ mkdir hello-docker-world
$ cd hello-docker-world
$ gem install sinatra --no-ri --no-rdoc
$ touch myapp.rb
$ open -e myapp.rb
```

Now add this to the myapp.rb file:
```
require 'sinatra'

get '/' do
  'Hello Docker World!'
end
```

Save and close the file.

Execute:
```sh 
$ ruby myapp.rb
```

Now open your browser and go to [http://localhost:4567](http://localhost:4567). You should see your "Hello Docker World!" page.

You have a running bare-bones Sinatra web app. Now let's dockerize it!

###### Step 2: Write your Dockerfile

Again, I shall shamelessly steal from Docker's [official intro](https://docs.docker.com/mac/step_four/).
```
"A Dockerfile describes the software that is 'baked' into an image. It isn’t just ingredients tho, it can tell the software what environment to use or what commands to run."
```

You'll want to do the following inside your `hello-docker-world` directory:
```sh
$ touch Dockerfile
$ open -e Dockerfile
```

Now add the following text to your Dockerfile:
``` 
FROM instructure/ruby-passenger:2.3

USER root

ENV APP_HOME /usr/src/app
RUN mkdir -p $APP_HOME
WORKDIR $APP_HOME

RUN gem install sinatra --no-ri --no-rdoc

COPY . $APP_HOME

ENV RACK_ENV=production

EXPOSE 80

CMD ruby myapp.rb -p 80
```

There we have our Docker recipe! Save and close the file.

The content of this Dockerfile tells Docker we're going to base our app on a prebuilt Ruby v2.3 image hosted by Instructure. It then creates the directory, `/usr/src/app`, where we'll store our app's code. Then it installs the sinatra gem, sets the `RACK_ENV` environment variable, and exposes port 80 for us (so that we can access the web server from outside the docker container). Finally, it runs the BASH command, `ruby myapp.rb -p 80` (specifying the port).

###### Step 3: Build your Image

Execute the following in your command line (don't forget the period . ):
```sh
$  docker build -t hello-docker-world .
```

If you'd like to learn what's happening here, read [this](https://docs.docker.com/mac/step_four/).

Now see your image on your machine:
```sh 
$ docker images
```

Cool!

Now for the moment of truth...
```sh 
$ docker run -e VIRTUAL_HOST=myapp.docker hello-docker-world
```

Go to http://myapp.docker in your browser. You should see "Hello Docker World!"

Success! You just built and ran your first dockerized app!  \o/

#### Bonus: Test your App in a dockerized Selenium Grid

Take a look at [docker-grid-nightwatch](https://github.com/mycargus/docker-grid-nightwatch) for more info.

