# Petstore API

A Petstore API a PetStore webapp (`bm_frontend`) es PetStore backend (`bm_backend`) kozotti API-t definialja. Emiatt mind NPM es Maven package-t is generalunk.

- *npm*: `https://bankmonitor-767398085282.d.codeartifact.us-east-1.amazonaws.com/npm/bm-apis/petstore`
- *maven*: `https://bankmonitor-767398085282.d.codeartifact.us-east-1.amazonaws.com/maven/bm-apis/petstore`

## Fejlesztesi ciklus

Az API modositasakor a kovetkezoket kell tenni:

1. `bm_pets.yml` modositasa
1. a `bm_pets.yml`, `pom.xml` es `package.json` -ban a `version` leptetese, szinkronizalasa
1. a generalas elvegzese, ellenorzes:
  - `npm run gen`
  - `mvn clean compile package`
1. pull request es code-review
1. a CI/CD automatikusan elkesziti a package kesziteset, teszteleset, breaking change detektalast, stb.

Ezek utan az API-t hasznalo applikaciokban (FE, BE) az uj verziot hasznalni (last Maven Pull es NPM Pull).

## Java Maven Push

Manualisan a kovetkezokeppen tudjuk a package-t deployolni (felteve, hogy az AWS CLI v2 telepitve es konfiguralva van az alabb hasznalt profillal):

    export CODEARTIFACT_AUTH_TOKEN=$(aws --profile leanflow codeartifact get-authorization-token --domain bankmonitor --query authorizationToken --output text)
    mvn -s settings.xml clean package deploy

## Java Maven Pull

A package hasznalatahoz a kovetkezo dependency-t kell felvenni a `pom.xml`-be:

    <dependency>
      <groupId>hu.bankmonitor.apis</groupId>
      <artifactId>petstore</artifactId>
      <version>1.0.6</version>
    </dependency>

Ezen kivul a `settings.xml`-be kell felvenni a repository elereset:

    <settings>
      <profiles>
        <profile>
          <id>bankmonitor-bm-apis</id>
          <activation>
            <activeByDefault>true</activeByDefault>
          </activation>
          <repositories>
            <repository>
              <id>bankmonitor-bm-apis</id>
              <url>https://bankmonitor-571188843450.d.codeartifact.eu-west-1.amazonaws.com/maven/bm-apis/</url>
            </repository>
          </repositories>
        </profile>
      </profiles>

      <servers>
        <server>
          <id>bankmonitor-bm-apis</id>
          <username>aws</username>
          <password>${env.CODEARTIFACT_AUTH_TOKEN}</password>
        </server>
      </servers>
    </settings>

# NPM Push

Manualisan a kovetkezokeppen tudjuk publisholni a package-t:

    export CODEARTIFACT_AUTH_TOKEN=$(aws --profile leanflow codeartifact get-authorization-token --domain bankmonitor --query authorizationToken --output text)
    npm publish

# NPM Pull

Az `.npmrc`-be be kell allitani a repository elereset:

    @bankmonitor:registry=https://bankmonitor-767398085282.d.codeartifact.us-east-1.amazonaws.com/npm/bm-apis/
    //bankmonitor-767398085282.d.codeartifact.us-east-1.amazonaws.com/npm/bm-apis/:always-auth=true
    //bankmonitor-767398085282.d.codeartifact.us-east-1.amazonaws.com/npm/bm-apis/:_authToken=${CODEARTIFACT_AUTH_TOKEN}

Az authentikacio token lekerese:

    export CODEARTIFACT_AUTH_TOKEN=$(aws --profile leanflow codeartifact get-authorization-token --domain bankmonitor --query authorizationToken --output text)

NPM pull:

    npm install @bankmonitor/petstore

# CI / CD

1. version sync check
1. API breaking change check
1. NPM build, test (par)
1. Maven compile, build, test (par)
1. Publish - csak akkor, ha minden elobbi step PASS.