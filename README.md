> # 🔒 lens <img align="right" width="126" src=".github/character.png">
>
> Let's Encrypt Nginx Server.

## Images

- [kamilsk/nginx](https://hub.docker.com/r/kamilsk/nginx/)
- [mirror at quay.io](https://quay.io/repository/kamilsk/nginx)

```bash
$ docker run --rm -d \
             -e LE_ENABLED=true -e LE_EMAIL=kamil@samigullin.info \
             -v certificates:/etc/nginx/ssl \
             kamilsk/nginx:alpine kamil.samigullin.info:samigullin.info,www.samigullin.info \
                                  www.octolab.org:octolab.org
```

## Development

You can add [self-signed](nginx/etc/ssl/local.crt) certificate to your keychain
to avoid getting annoying browser privacy errors on local development.

```bash
$ make nginx-up nginx-status

       Name                 Command          State                    Ports
---------------------------------------------------------------------------------------------
experiments_nginx_1   nginx -g daemon off;   Up      80/tcp
experiments_proxy_1   /entrypoint.sh         Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp

$ open https://127.0.0.1.xip.io/
$ curl -v http://127.0.0.1.xip.io/
# > GET / HTTP/1.1
# > Host: 127.0.0.1.xip.io
# > User-Agent: curl/7.54.0
# > Accept: */*
# >
# < HTTP/1.1 308 Permanent Redirect
# < Server: nginx
# < Date: Tue, 15 May 2018 14:01:52 GMT
# < Content-Type: text/html; charset=utf-8
# < Content-Length: 180
# < Connection: keep-alive
# < Location: https://127.0.0.1.xip.io/
# < Strict-Transport-Security: max-age=86400
# < X-UA-Compatible: IE=Edge
# < X-Frame-Options: SAMEORIGIN
# < X-Content-Type-Options: nosniff
# < X-XSS-Protection: 1; mode=block
# < Cache-Control: no-transform
# <
$ curl -v --insecure https://www.127.0.0.1.xip.io/
# > GET / HTTP/2
# > Host: www.127.0.0.1.xip.io
# > User-Agent: curl/7.54.0
# > Accept: */*
# >
# < HTTP/2 308
# < server: nginx
# < date: Wed, 16 May 2018 08:24:03 GMT
# < content-type: text/html; charset=utf-8
# < content-length: 180
# < location: https://127.0.0.1.xip.io/
# < strict-transport-security: max-age=86400
# <
$ make nginx-down
```

## Open questions

- [ ] How to be with `ssl.no-default`? Which certificate should be chosen in production?
- [ ] How to test automatically?

## To-do list

- [ ] Renew certificates automatically without any extra overhead
- [ ] Force renew certificates
- [ ] Reduce Let's Encrypt API calls (rate limit)
  - https://github.com/umputun/nginx-le/pull/14
  - --renew-by-default
- [ ] Security improvement
  - Up test result to A+ instead of A (in SSL Server Test by Qualys SSL Labs)
  - Review https://github.com/h5bp/server-configs-nginx/issues/180
  - Review https://github.com/h5bp/server-configs-nginx/pull/183
  - Review https://github.com/h5bp/server-configs-nginx/pull/190
- [ ] 2.x
```bash
$ docker run --rm -d kamilsk/nginx [/entrypoint.sh] [run]                               # instead of `process`
$ docker run --rm -d kamilsk/nginx [/entrypoint.sh] renew                               # runs `certbot renew --force-renewal`
$ docker run --rm -d kamilsk/nginx [/entrypoint.sh] repeat                              # runs `with` again
$ docker run --rm -d kamilsk/nginx [/entrypoint.sh] with domain1:alias1,alias2 domain2  # stores `repeat.log`
$ docker run --rm -d kamilsk/nginx [/entrypoint.sh] bash                                # exec "$@" otherwise
```

---

made with ❤️ for everyone
