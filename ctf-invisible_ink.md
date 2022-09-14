# Challenge: Invisible Ink

[Snyk CTF challenge](https://101.ctf-snyk.io/challenges#Invisible%20Ink-1)

## Target

http://invisible-ink.c.ctf-snyk.io/

Supplied files:
- index.js
- package.json

## Attempts

- browse to link
  - `POST /echo`
  - `Content-Type: application/json`
  - body: `{"message": "ping"}`
  - response: `{"userIP": "127.0.0.1", "time": 1608203140926, "message": "ping", "flag": "disabled"}`

The `flag` property is interesting. Try to enable it.



