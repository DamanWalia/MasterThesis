# Self-Sovereign Identity Management

## Prerequisite:
- JDK 16 build environment
- Gradle
- Docker
- yarn

## Setting up Keycloak 
- Clone the repo and execute following commands.
```shell
cd Keycloak
docker compose --detach --file kecloak.yml up
```

## Setting up Wallet Kit 
```shell
cd ../waltid-walletkit/docker/
docker compose -d up
```

## Setting up IDP Kit and client registration
```shell
cd ../waltid-idpkit/
./gradlew build install
alias idpkit="build/install/waltid-idpkit/bin/waltid-idpkit"
idpkit config --oidc clients register -n "keycloak" -r "http://localhost:8004/realms/MA/broker/ssi/endpoint"

# -n - Name of the client
# -r - The redirect URL which should be used in the OIDC flow
#--all-redirect-uris - Redirect can go to any URL specified in the OIDC flow requests
```
- Copy client_id and client_secret and save it to use it later to integrate IDP kit with Keycloak.

```shell
idpkit config --oidc clients register -n "myRegistration" -r "https://applicationName.com" -u "client_id"

# -n - Name of the client
# -r - The redirect URL which should be used in the OIDC flow
# -u - The ID of the client we would like to update
```

## Execute IDP Kit
```shell
idpkit run

open a new terminal
cd waltid-idpkit/web/waltid-idpkit-ui/
yarn install 
yarn dev
```

- Open IDP kit's OIDC configuration at http://localhost:8080/api/oidc/.well-known/openid-configuration and save the configuration in local.

## Configure Keycloak
- Open Keycloak at http://localhost:8004
- Login to master realm as an Admin.
```
Username: admin
Password: test
```

- Create a realm for SSIdM.
- Select OpendID Connect v1.0 in identity provider to add SSI as IdP, use import from file functionality to import IdP configuration from previously saved file.
- Add client_id and client_secret in the fields. Use value "oidc vp_token" and "none" in Default Scopes and Prompt respectively. And save the IdP.

- SSIdM is available on http://localhost:8004/realms/MA/account/