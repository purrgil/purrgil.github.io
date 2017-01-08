# Purrgil Package
A package is **ANYTHING** that you can pull from somewhere! The model from a package it's simple: You have a **name** a alias from the folder name (that represents your namespace), the **identity** how the property name says, this is a ID from your package, a unique string that usually be a github `<name>/<repo>` or a dockerhub image name. To finish we have package download properties as `provider` to identify the download source (github, bitbucket or dockerhub), a ssh flag and if the package will turn a service into dockercompose :)


### Where I use that?
Packages are saved into your `purrgil.yml` with the purpose of remember the sources from mount your development environment and in the future use the data for construct images and deploy them.


# How it works
A package is a go struct find [here](https://github.com/purrgil/purrgil/blob/master/file/purrgilpkg.go)

```golang
    type PurrgilPackage struct {
        Name     string `yaml:"name"`
        Identity   string `yaml:"identity"`
        Provider string `yaml:"provider"`
        Service  bool   `yaml:"service"`
    }
```
We use that mainly into PurrgilConfig struct [here](https://github.com/purrgil/purrgil/blob/master/file/purrgil.go)

```golang
type Purrgil struct {
    yaml         Yaml
    Name       string           `yaml:"name"`
    Packages []PurrgilPackage `yaml:"packages,omitempty"`
    Settings    PurrgilSettings  `yaml:"settings,omitempty"`
}
```

In the rest of code we use `PurrgilPackage` unique as a type, to invoke that in most of cases we use method `NewPurrgilPackage(string, AddConfig{})` to create a instance. The `AddConfig` can be found [here](https://github.com/purrgil/purrgil/blob/master/configs/commands.go) and brings inside the command `purrgil add` settings:

```golang
type AddConfig struct {
    IsService             bool
    Provider              string
    HttpsMode          bool
    CustomName     string
    ComposeConfig bool
}
```
