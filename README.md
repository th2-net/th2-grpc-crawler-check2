# th2 gRPC crawler data processor library

This project contains the gRPC interface to implement if you want to create your own crawler data processor.

The crawler data processor work with the [crawler](https://github.com/th2-net/th2-crawler)

## How to transform template
1. Create a directory with the same name as project name (use underscores instead of dashes) under `src/main/proto` directory (remove other files and directories if they exist).
2. Place your custom `.proto` files in created directory. Pay attention to `package` specifier and `import` statements.
3. Edit `release_version` and `vcs_url` properties in `gradle.properties` file.
4. Edit `rootProject.name` variable in `settings.gradle` file. This will be the name of Java package.
5. Edit `package_info.json` file in order to specify name and version for Python package (create file if it's absent).
6. Edit parameters of `setup.py` in `setup` function invocation such as: `author`, `author_email`, `url`. Do not edit the others.
7. Edit `README.md` file according to the new project.

Note that the name of created directory under `src/main/proto` directory is used in Python (it's a package name).

## How to maintain project
1. Make your changes.
2. Up version of Java package in `gradle.properties` file.
3. Up version of Python package in `package_info.json` file.
4. Commit everything.

## How to run project

### Java
If you wish to manually create and publish package for Java, run these command:
```
gradle --no-daemon clean build publish artifactoryPublish \
       -Pbintray_user=${BINTRAY_USER} \
       -Pbintray_key=${BINTRAY_KEY}
```
`BINTRAY_USER` and `BINTRAY_KEY` are parameters for publishing.

### Python
If you wish to manually create and publish package for Python:
1. Generate services by gradle:
    ```
       gradle --no-daemon clean generateProto
    ```
    You can find the generated files by following path: `src/gen/main/services/python`
2. Generate code from `.proto` files and publish everything:
    ```
    pip install -r requirements.txt
    python setup.py generate
    python setup.py sdist
    twine upload --repository-url ${PYPI_REPOSITORY_URL} --username ${PYPI_USER} --password ${PYPI_PASSWORD} dist/*
    ```
    `PYPI_REPOSITORY_URL`, `PYPI_USER` and `PYPI_PASSWORD` are parameters for publishing.

## Changes:

### v0.2.0 (Breaking changes)

#### Changed:

+ Use list of `MessageID` instead of mapping between session and `MessageID`.
  User now will have to specify `MessageID` for both directions in the response if he or she needs to set a checkpoint.
  The list should contain a single `MessageID` for each pair `alias + direction`.
  If more than one is found the last one (according to their sequences) will be taken.