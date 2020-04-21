# kubernetes-resources
Resources to learn Kubernetes

## Learn

- Online Course by the Linux Foundation: [Introduction to Kubernetes](https://www.edx.org/course/introduction-to-kubernetes).
  swi recommended to start your journey!
- [CKAD exercises](https://github.com/dgkanatsios/CKAD-exercises): exercises for the [CKAD](https://www.cncf.io/certification/ckad/) certification. Also useful as cheat sheet.
- [Official kubernetes documentation](https://kubernetes.io/docs/home/) (start-your-journey-approved by cle)
- [Kubernetes Security Talk](https://github.com/luxeria/slides/blob/master/2020-04-01/Kubernetes_Security_gandro.pdf)

### Not yet read / tested

- [Certified Kubernetes Administrator (CKA) Preparation Guide - Curriculum V1.14](https://github.com/leandrocostam/kubernetes-certified-administrator-prep-guide)
- [Talks about Kubernetes](https://media.ccc.de/search/?q=kubernetes) by media.ccc.de
- [More talks about Kubernetes](https://fosdem.org/2020/search/?q=kubernetes) by FOSDEM

## Environment

### Easy to install/use distributions

- [k3s](https://k3s.io/): single binary kubernetes with containerd and kubectl included. Supports multi-node clusters. \
❤️ swi's favourite ❤️
- [microk8s](https://snapcraft.io/microk8s): all-in-one install, requires snapd. Supports multi-node clusters.

### In the cloud

No lab setup at home?

-  [GKE](https://cloud.google.com/kubernetes-engine/): You start with $300 worth of credits for free🎉

## Tips and Tricks

### Use `kubectl explain`

Kubectl has a built in help for every possible field in your manifest similar to `man`.

```sh
$ kubectl explain cronjob.spec.jobTemplate.spec
KIND:     CronJob
VERSION:  batch/v1beta1

RESOURCE: spec <Object>

DESCRIPTION:
     Specification of the desired behavior of the job. More info:
[...]

FIELDS:
   activeDeadlineSeconds	<integer>
     Specifies the duration in seconds relative to the startTime that the job
[...]
```

## Common Misconceptions

### Persistent Volume (Claim) Access Modes

The access modes (`ReadWriteOnce`, `ReadOnlyMany`, and `ReadWriteMany`) are about the nodes, not the pods. A `ReadWriteOnce` volume can still be read-write mounted by many pods, but only by pods on the same node.

## FAQ

### Why Does My Containerized (Perl) App Not Handle Signals Correctly?

OR: If starting my (Perl) app in a container `CTRL-C` does no longer work.

Docker creates a new PID namespace for each container. The first process in the container
has the process id 1.

> Note: A process running as PID 1 inside a container is treated specially by Linux: it ignores any signal with the default action. So, the process will not terminate on SIGINT or SIGTERM unless it is coded to do so.
> - [Docker Docs](https://docs.docker.com/engine/reference/run/#foreground)

A good cross-platform solution is running a tiny init system in front of the app. See [solution](perl-init/).
