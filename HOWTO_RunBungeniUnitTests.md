# Running all the tests

From the buildout root folder run :

```
./bin/test bungeni
```

# Running tests for a package

From the buildout root folder run :

```
./bin/test bungeni.core
```
Where bungeni.core is the package name


# Running a specific test within a package

From the buildout root folder run :
```
./bin/test bungeni.core -t question.txt
```

Where bungeni.core is the package name and 'question.txt' is the specific test file.