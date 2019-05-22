```shellsession
$ docker build . -f Dockerfile.04-01.unfavorable --no-cache
$ docker build . -f Dockerfile.04-02.favorable --no-cache
```

```shellsession
$ docker image ls | grep 'f54b6bf3f1f5\|80cea2562685'
```
