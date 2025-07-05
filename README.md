# Run camel route on linux

## Prerequisites

* Java JDK
* Apache maven
* jbang with camel app

## Create route

* Create directory
* create route with `camel init http-demo.camel.yaml`
* Replace file content with:
```bash
- route:
    from:
      uri: "platform-http:/hello"
      steps:
        - setBody:
            constant: "Hello from Camel JBang!"
```

## Test route

* run route with `camel run http-demo.camel.yaml`
* test route with `curl -v localhost:8080/hello`

## Export route

* export to quarkus project:

```bash
camel export --runtime=quarkus --gav=ch.dulce:http-demo:1.0 http-demo.camel.yaml 
```

* package and run project
```bash
mvn package

java -jar target/quarkus-app/quarkus-run.jar
```

## Deploy to Github Packages

* Add `distributionManagement` section to pom.xml replacing with your user name or organisation

```bash
<distributionManagement>
  <repository>
    <id>github</id>
    <name>GitHub Packages</name>
    <url>https://maven.pkg.github.com/<github user>/http-demo</url>
  </repository>
</distributionManagement>
```

* create workflow file `.github/workflows/release.yaml`

```bash
name: Publish package to GitHub Packages
on:
  release:
    types: [created]
jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: "17"
          distribution: "temurin"
      - name: Publish package
        run: mvn --batch-mode deploy
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
