# Add
`add` will do exactly what you expect: add a package, if you not know what is a purrgil package [click here](/introduction/package.md). This can be a github's repo or a dockerhub's image. Example: `purrgil/purrgil` (github) or `node:6` (dockerhub).
By the default purrgil will expect a github's repo, if you want to use a dockerhub's image or other thing you've to specify using flag `--provider`.

```
purrgil add <package> [flag]
```
### Flags
...todo

## How it works
First of all you need understanding the reason of packages, the link was passed before in add intro, once make that you can access the add code [here](https://github.com/purrgil/purrgil/blob/master/commands/add.go). Add start on [main.go](https://github.com/purrgil/purrgil/blob/master/main.go), we can find the `vars` with configuration and the call to `command.Add` into it:

```golang
    add     = app.Command("add", "Add a dependency to project")
    addS    = add.Arg("pkg", "Add a service").String()
    addNs   = add.Flag("not-a-service", "Add only a git repository").Bool()
    addDk   = add.Flag("dockerhub", "Install image directly from dockerhub").Bool()
    addName = add.Flag("name", "Give a custom name to package").String()
    addDcConfig  = add.Flag("compose-helper", "Active an interface to inject basic compose infos").Bool()
```
```golang
    case add.FullCommand():
        commands.Add(*addS, configs.AddConfig{
            IsService:  *addNs,
            Dockerhub:  *addDk,
            CustomName: *addName,
            ComposeConfig: *addDcConfig,
        })
```

After that we call our `docker-compose.yml`, `purrgil.yml` and `.gitignore` file objects to persist the changes, we use `NewPurrgilPackage` to instance a package and after that we user `PackageInstall(PurrgilPackage)` to access a single method of `install` command and download the source.

```golang
    path, _ := os.Getwd()

    purrgilconfig := file.NewPurrgil(path, "")
    dockercompose := file.NewDockerCompose(path)
    gitignore := file.NewGitIgnore(path)
    purrgilNewPackage := file.NewPurrgilPackage(pkgId, opts)

    PackageInstall(purrgilNewPackage)

    purrgilconfig.AddPackage(purrgilNewPackage)
    gitignore.AddIgnoredPath(purrgilNewPackage.Name)
```

If the flag `--compose-helper` be called, we call a shell form to get some informations to populate the `docker-compose`

```golang
    if opts.ComposeConfig {
        callComposeConfigInterface(purrgilNewPackage, &dockercompose)
    }
```

the `callComposeConfigInterface` is a simple method to verify if flag `--not-a-service` was called to know what kind of link the interface will get informations:

```
func callComposeConfigInterface(pkg file.PurrgilPackage, dc *file.DockerComposeFile) {
    if pkg.Service {
        serviceName, service := ishell.CollectDockerServiceInfo(pkg)
        dc.AddService(serviceName, service)
    } else {
        packages := ishell.CollectLinkPossibility(pkg)
        dc.LinkInService(pkg.Name, packages)
    }
}
```

After got all configurations, and set all objects, we called method `SaveFile` to persist the changes os the files. To know more about file objects you can read the code [here](https://github.com/purrgil/purrgil/blob/master/file)
