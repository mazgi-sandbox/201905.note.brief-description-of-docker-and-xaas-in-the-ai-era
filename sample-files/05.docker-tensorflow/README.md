## Prepare

```shellsession
$ ssh-keygen -f .ssh/deploy-key.id_rsa -C 'for demonstration'
```

## Demonstration

```shellsession
$ docker build . -f Dockerfile.05-01.fail

(omitted)

Cloning into 'piphello'...
Warning: Permanently added the RSA host key for IP address '13.114.40.48' to the list of known hosts.
Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
The command '/bin/sh -c apt update ****(omitted)**** && rm -rf /var/lib/apt/lists/*' returned a non-zero code: 128
```

### In-secure demonstration

```shellsession
$ docker build . -f Dockerfile.05-02.insecure

(omitted)

Cloning into 'piphello'...
Warning: Permanently added the RSA host key for IP address '52.192.72.89' to the list of known hosts.
Obtaining file:///tf/piphello
Installing collected packages: piphello
  Running setup.py develop for piphello
Successfully installed piphello
WARNING: You are using pip version 19.1, however version 19.1.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Removing intermediate container ec34051d4d4d
 ---> dd4ef160b6fb
Successfully built dd4ef160b6fb
```

```shellsession
$ docker history dd4ef160b6fb | grep COPY
f8ebcb8dcc6a        18 minutes ago      /bin/sh -c #(nop) COPY file:b8126180a0f71f0c…   1.82kB              
<missing>           2 weeks ago         /bin/sh -c #(nop) COPY file:7bc7e4b8c1418d53…   69B                 
<missing>           2 weeks ago         /bin/sh -c #(nop) COPY file:da7319f468506ee9…   1.77kB              
```

```shellsession
$ docker run -it f8ebcb8dcc6a sha1sum /root/.ssh/id_rsa
fd05b015547da6218b81000b4fe977b305e12c0a  /root/.ssh/id_rsa
$ shasum -a 1 .ssh/deploy-key.id_rsa
fd05b015547da6218b81000b4fe977b305e12c0a  .ssh/deploy-key.id_rsa
```

### Secure demonstration with BuildKit

```shellsession
$ DOCKER_BUILDKIT=1 docker build . --secret id=github_private_key,src=$(pwd)/.ssh/deploy-key.id_rsa -f Dockerfile.05-03.secure-with-buildkit

(omitted)

=> => writing image sha256:acf15fad4655d30293b158d9af1c771144b1e4503f8cbd091f40022d1bf038a6
```

```shellsession
>>> docker history acf15fad4655d30293b158d9af1c771144b1e4503f8cbd091f40022d1bf038a6 | head -4
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
acf15fad4655        2 minutes ago       RUN /bin/sh -c apt update       && apt insta…   36MB                buildkit.dockerfile.v0
<missing>           2 weeks ago         /bin/sh -c #(nop)  CMD ["bash" "-c" "source …   0B                  
<missing>           2 weeks ago         |6 PIP=pip3 PYTHON=python3 TF_PACKAGE=tensor…   3.42kB              
```
