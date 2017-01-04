# Quick Start

### Init

First of all you need to create a project with a unique name! The project name is use to prefix their containers, so think about that:

```
purrgil init my-mult-container-env
```

After this a folder `my-mult-container-env` will be created with `docker-compose.yml`, `purrgil.yml` and a `.gitignore`.

### Add

```
purrgil add purrgil/flask-docker-app --compose-helper
```

The flag `--compose-helper` will help us to insert in compose some information, reponse the questions:

```
purrgil add mongo --dockerhub --compose-helper
```

The flag `--dockerhub` indentify that the provider for this package isn't a repository. The unique response that you need to give here is:

### Running

To run the application we can give a simple command in root of app:
```
purrgil up
```

Know you can access: `docker-machine-ip:port` and see the magic and when you want to drop only the containers of your purrgil app:
```
purrgil down
```


