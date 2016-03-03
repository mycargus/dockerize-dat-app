# dockerize-dat-app
A brief, hands-on, high-level introduction to Docker.

This is *not* a substitute for Docker's [official tutorial](https://training.docker.com/self-paced-training). Rather, I'll attempt to briefly explain what Docker has to offer. Then we're going to dive right in to the command line and make some Docker magic. :magic:

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

First, you need to install [dinghy](https://github.com/codekitchen/dinghy) and its dependencies. Read dinghy's documentation for a full explanation if you like. Just know it will make your Dockerized life much easier.

Once you have dinghy up and running (no, really--be sure you've executed the terminal command, `dinghy up`), you're ready to move forward.

### Hello Docker World

###### Step 1: Create your App

For this example we'll use Ruby on Rails. No need for a database or testing.

```sh 
$ rails new hello-docker-world --skip-activerecord --skip-test-unit
```

Follow Rails's instructions as outlined in its default welcome page to display "Hello Docker World!" when the app loads. 

Now for a sanity check:
```sh 
$ bundle install
$ rails s
$ open http://localhost:3000
```

You should see your "Hello Docker World!" page.

###### Step 2: Write your Dockerfile

Again, I shall shamelessly steal from Docker's [official intro](https://docs.docker.com/mac/step_four/).
```
"A Dockerfile describes the software that is 'baked' into an image. It isn’t just ingredients tho, it can tell the software what environment to use or what commands to run. Your recipe is going to be very short."
```

Now do this:
```sh
$ touch Dockerfile
$ open -e Dockerfile
```

Now add the following text to your Dockerfile:
``` 
FROM rails:onbuild

USER root

ENV APP_HOME /usr/src/app
RUN mkdir -p $APP_HOME
WORKDIR $APP_HOME

COPY . $APP_HOME

ENV VIRTUAL_HOST=hello-docker-world.docker

RUN groupadd -r docker && useradd -r -g docker docker

RUN chown -R docker:docker $APP_HOME
USER docker
```

There we have our Docker recipe! Save and close the file.

The content of this Dockerfile tells Docker we're going to base our app on the official Rails image. Then it creates the directory, `/usr/src/app`, where we'll store our app's code. Next, it sets the `VIRTUAL_HOST` environment variable (this lets dinghy know our app's URI). Finally, it creates a `docker` user and switches to that user for us.

###### Step 3: Build your Image

Execute the following in your command line:
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
$ docker run hello-docker-world
$ open http://hello-docker-world.docker
```

You should see "Hello Docker World".

Success! You've just built and ran your first dockerized app!  \o/

#### Bonus: Test your App in a dockerized Selenium Grid

Checkout my project, [docker-grid-nightwatch](https://github.com/mycargus/docker-grid-nightwatch) for more info.

