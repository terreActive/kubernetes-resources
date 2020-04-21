# Solve Signal Handling in Perl Containers with `tini`

We should be able to containerize this simple [Perl application](app.pl) with
a minimal Docker file.

```Dockerfile
FROM perl

ADD app.pl /

ENTRYPOINT ["perl", "app.pl"]
```

Lets try it out:

```bash
$ docker build .
[...]
Successfully built e5b19c53b724

$ docker run --rm -it e5b19c53b724
Gonna sleep forever...
^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C^C
```

Looks good! But why doesn't `CTRL-C` kill my application?

Docker creates a new PID namespace for each container. The first process in the container has PID 1.

> Note: A process running as PID 1 inside a container is treated specially by Linux: it ignores any signal with the default action. So, the process will not terminate on SIGINT or SIGTERM unless it is coded to do so.
> - [Docker Docs](https://docs.docker.com/engine/reference/run/#foreground)

A good cross-platform solution is running a tiny init system in front of the app. [`tini`](https://github.com/krallin/tini) is a good candidate and even used by Docker itself. Let's use it:

```diff
 FROM perl
 
+RUN apt-get update -q && apt-get install tini
+
 ADD app.pl /
 
-ENTRYPOINT ["perl", "app.pl"]
+ENTRYPOINT ["tini" , "--", "perl", "app.pl"]
```

Lets try it out:

```bash
$ docker build .
[...]
Successfully built 93b6ca67c66a

$ docker run --rm -it 93b6ca67c66a
Gonna sleep forever...
^C
```

It works 🥳.
