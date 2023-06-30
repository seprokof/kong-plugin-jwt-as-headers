[![Luacheck](https://github.com/seprokof/kong-plugin-jwt-as-headers/workflows/Lint/badge.svg)](https://github.com/seprokof/kong-plugin-jwt-as-headers/actions/workflows/lint.yml)
[![Unix build](https://img.shields.io/github/actions/workflow/status/seprokof/kong-plugin-jwt-as-headers/test.yml?branch=master&label=Test&logo=linux)](https://github.com/seprokof/kong-plugin-jwt-as-headers/actions/workflows/test.yml)
[![Coverage Status](https://coveralls.io/repos/github/seprokof/kong-plugin-jwt-as-headers/badge.svg?branch=master)](https://coveralls.io/github/seprokof/kong-plugin-jwt-as-headers?branch=master)

# kong-plugin-jwt-as-headers

The plugin exposes JWT claims as HTTP headers attached to request. Please note, that plugin does not do validation 
of JWT tokens. For that purpose you need to use one of authentication plugins. 

## How it works

When enabled, this plugin will add new HTTP headers to request based on JWT claims provided in the request. 
The generated headers follow the naming convention of `<generated_header_prefix>-<capitalized-Claim-name>[-<capitalized-nested-Claim-name>]`.
For example for payload of JWT token generated by Keycloak

```json
{
    "exp": 1683330464,
    "iat": 1683330164,
    "auth_time": 1683330164,
    "jti": "bfa22188-578b-4b36-b4eb-ae6565ffdeb6",
    "iss": "https://auth.company.com/realms/company",
    "aud": "account",
    "sub": "6b93885f-2cf9-49f3-a6ce-80ba9de83445",
    "typ": "Bearer",
    "azp": "kong",
    "nonce": "aar22yqb822b3e184fde41t32630d435",
    "session_state": "6edf0280-1624-49b8-b902-a4c58bc878f82",
    "acr": "1",
    "allowed-origins": [
        ""
    ],
    "realm_access": {
        "roles": [
            "default-roles-company",
            "offline_access",
            "uma_authorization"
        ]
    },
    "resource_access": {
        "account": {
            "roles": [
                "manage-account",
                "manage-account-links",
                "view-profile"
            ]
        }
    },
    "scope": "openid profile email",
    "sid": "6edf0280-1624-49b8-b902-a4c58bc878f82",
    "email_verified": true,
    "name": "John Doe",
    "preferred_username": "jdoe",
    "given_name": "John",
    "family_name": "Doe",
    "email": "john.doe@company.com"
}
```

the following headers will be generated using default configuration

```
Claim-Exp: 1683330464
Claim-Iat: 1683330164
Claim-Auth_time: 1683330164
Claim-Jti: bfa22188-578b-4b36-b4eb-ae6565ffdeb6
Claim-Iss: https://auth.company.com/realms/company
Claim-Aud: account
Claim-Sub: 6b93885f-2cf9-49f3-a6ce-80ba9de83445
Claim-Typ: Bearer
Claim-Azp: kong
Claim-Nonce: aar22yqb822b3e184fde41t32630d435
Claim-Session_state: 6edf0280-1624-49b8-b902-a4c58bc878f82
Claim-Acr: 1
Claim-Allowed-origins: 
Claim-Realm_access-Roles: default-roles-company, offline_access, uma_authorization
Claim-Resource_access-Account-Roles: manage-account, manage-account-links, view-profile
Claim-Scope: openid profile email
Claim-Sid: 6edf0280-1624-49b8-b902-a4c58bc878f82
Claim-Email_verified: true
Claim-Name: John Doe
Claim-Preferred_username: jdoe
Claim-Given_name: John
Claim-Family_name: Doe
Claim-Email: john.doe@company.com
```

## Installation

Currently only manual installation is possible.

1. Clone the repo
2. Compile and install package
`luarocks make`
3. Include `jwt-as-headers` in your `KONG_PLUGINS` environment variable
4. Reload Kong

For additional details you may also check [official documentation](https://docs.konghq.com/gateway/latest/plugin-development/distribution/#manually).   

## Configuration

Configuration of plugin can be enabled or updated using Admin API performing the following request:

```bash
$ curl -X POST http://kong:8001/services/<service-name-or-id>/plugins \
    -d "name=jwt-as-headers" \
    -d "config.token_header_name=Authorization" \
    -d "config.generated_header_prefix=Claim"
```

Table below describes all possible configuration options and their default values.

Parameter|Default|Description
---|---|---
`token_header_name`|`Authorization`|The name of request's HTTP header containing JWT token.
`generated_header_prefix`|`Claim`|Prefix for the names of generated headers.