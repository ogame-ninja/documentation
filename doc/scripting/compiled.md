# Compiled script

**Compiled script is experimental and subject to major changes**

### Why compiled script ?

- You can share a script with other users without sharing the sources/logic.
- You can build simple user interfaces to configure the script.

### Scaffold new project

```
./nja scaffold new_project
```

Scaffolding a new project will create two files:

- `config.xml` this is where you configure the user interface
- `main.ank` this is the entry point for the script

### Compile project

To compile a project into a single `.nja` file, run the following command

```
./nja compile new_project
```

You can share this `.nja` file with other users without having to share the sources of the script.

### How to develop

One way of doing it would be to:

- Develop, validate and test your script on the scripts tab in nja
- Once you are happy with the result, remove the configuration variables from the script, and create your xml UI
- Then you can copy the remaining script in `nain.ank` inside your project
- And then compile the `project.nja` file

Tooling are still being worked on to improve this process.