# Motivation

In final of 2016 we start migrate some stuff of our enviroment to Docker in enterprise, talk and read about that we found some problems and nice ways to resolve that. At some talks we discuss about create some scripts to make our work easily while we develop and deploy our services. So I create Purrgil to make those scripts and help people to create your enviroments faster!

### Problems
We work in different environments and many times we need too change things on our local env to make things be much equal as we can. So we decided to use docker, but how we can improve our time in install the total env, list all dependencies and after all use that to development and production?

### Solution
Let's list our dependencies and get some important information for in a certain moment we can use that for make things easy, after this we can administrate docker-compose using prefixes and treat the conections with a kind of namespace.
