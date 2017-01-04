# Slow Start

### Init

Init go a little far from only init a folder, he can get your git stuff if you already create a github repository, example:

Us for purrgil create a repo called `purrgil/purrgil-example` to can put quick start example to run! So we try something like this:

```
purrgil init my-mult-container-env --github purrgil/purrgil-example
```

 We continous with the "same" result, a folder `my-mult-container-env` will be created with `docker-compose.yml`, `purrgil.yml` and a `.gitignore`. But know we have a origin setted to github, our project are done to be versioned!

### Add

Like in [Quick Start]() we will add two repos! Check they if you forgot the configuration answers!

```
purrgil add purrgil/flask-docker-app --compose-helper

purrgil add mongo --dockerhub --compose-helper
```

But know we will use another flag! I have an github repository that ISN'T a docker service, they can be a internal lib, a util repository with mocks or anything that you use to develop and need to transform in a volume. You can demonstrate that using flag: `--not-a-service`. If you put `--compose-helper` together they will ask you for link this repo with another services!

```
purrgil add purrgil/data-sample --not-a-service --compose-helper
```

And response the service `db` as a link!

### Packages
To confeer if all are ok in your env, you can use `purrgil packages` to list the purrgil packages installed in your env! They have some filter flags if you want. You can check using `--help` ou in [documentation]()


### Versioning
After add the things you can use `github` commands to versioned your environment or you can use `push` for a simple commit:

```
purrgil push "my commit message"
```

This command will do a simple commit/push with needed files [here you can understand better]()! If you need some more complex use `git` api <3

### Running

As in [Quick Start]() we up/down our repo with basic commands:

```
purrgil up

purrgil down
```

