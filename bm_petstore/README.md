# Publish Maven package

export CODEARTIFACT_AUTH_TOKEN=$(aws --profile leanflow codeartifact get-authorization-token --domain bankmonitor --query authorizationToken --output text)
mvn -s settings.xml clean package deploy

# Publishing NPM package

export CODEARTIFACT_AUTH_TOKEN=$(aws --profile leanflow codeartifact get-authorization-token --domain bankmonitor --query authorizationToken --output text)
npm publish

# CI required tests

- Test that the package.json and pom.xml versions are the same.
- API breakage tests