# Add
`add` will do exactly what you expect: add a package, if you not know what is a purrgil package [click here](/introduction/package.md). This can be a github's repo or a dockerhub's image. Example: `purrgil/purrgil` (github) or `node:6` (dockerhub).
By the default purrgil will expect a github's repo, if you want to use a dockerhub's image or other thing you've to specify using flag `--provider`.

```
purrgil add <package> [flag]
```
### Flags

**not-a-service**: Sometimes you need to add something that isn't a service (not have a dockerfile), can be unique a repo with mock data or a internal lib create to abstract and reuse a code, a open source project that you use but always change de code and need more flexibility to develop inside docker environments. When you use that we list the dependencie but in `--compose-helper` we try to link that as a volume in another files :)

**provider**: By default add will get things from `github`, but you can change that passing this flag! Actually we are able to get from `bitbucket` (mercurial supported) and `dockerhub`. Enums: `bitbucket`,`bitbucket_mcr` and `dockerhub`

**https**: By default clones will try to persist on a `SSH` url, if you want the `https` protocol you can use that flag.

**name**: Always the name will be the repo name or image name. Example: `purrgil/something-example` -> `something-example`, if you want change that you can use this flag and pass a custom name

**compose-helper**: This functionallity will help you to insert some basic data into your `docker-compose` file, by default purrgil only persist thins into `purrgil.yml` and `.gitingnore` this flag change that.

## How it works
First of all you need understanding the reason of packages, the link was passed before in add intro, once make that you can access the add code [here](https://github.com/purrgil/purrgil/blob/master/commands/add.go). Add start on [main.go](https://github.com/purrgil/purrgil/blob/master/main.go), we can find the `vars` with configuration and the call to `command.Add` into it:

```golang
    avaibleProviders = []string{"github", "bitbucket", "dockerhub", "bitbucket_mcr"}

    add     = app.Command("add", "Add a dependency to project")
    addPkgIdentity = add.Arg("pkg", "Add a service").String()
    addServiceFlag  = add.Flag("not-a-service", "Add only a git repository").Bool()
    addProvider   = add.Flag("provider", "Install image directly from dockerhub").Enum(avaibleProviders...)
    addName = add.Flag("name", "Give a custom name to package").String()            addName = add.Flag("name", "Give a custom name to package").String()
    addHTTPS = add.Flag("https", "download package in https mode").Bool()
    addComposeConfig  = add.Flag("compose-helper", "Active an interface to inject basic compose infos").Bool()
```
```golang
    case add.FullCommand():
        commands.Add(*addPkgIdentity, configs.AddConfig{
            IsService:  *addServiceFlag,
            Provider:  *addProvider,
            CustomName: *addName,
            ComposeConfig: *addComposeConfig,
            HttpsMode: *addHTTPS,
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
