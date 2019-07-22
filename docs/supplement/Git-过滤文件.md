## .gitattributes

```xml
# Windows-specific files that require CRLF:
*.bat       eol=crlf
*.txt       eol=crlf

# Unix-specific files that require LF:
*.java      eol=lf
*.sh        eol=lf
```

## .gitignore

```text
target/
!.mvn/wrapper/maven-wrapper.jar

## STS ##
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans

## IntelliJ IDEA ##
.idea
*.iws
*.iml
*.ipr

## JRebel ##
rebel.xml

## MAC ##
.DS_Store

## Other ##
logs/
temp/
```