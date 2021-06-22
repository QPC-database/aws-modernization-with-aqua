+++
title = "Drift Prevention"
date = 2020-06-16T19:01:12-04:00
weight = 11
chapter = true
pre = "<b></b>"
+++

## Drift Prevention
By definition, cloud-native workloads are immutable in nature. This immutability principle is heavily leveraged for defining the cloud-native security strategy in the form of drift prevention.

**Drift Prevention** prohibits running files that are not a part of the original image from running, ensuring no changes are made to the image after it is instantiated into a container. This prevents hackers from downloading new malicious code to the running container. 

Let's first access the running container by running this command to exec into the nginx container.
```shell
kubectl exec -it <pod_name> bash
```

Now, to simulate a drift, we will copy an allowed executable, like ls, to another name like list
```shell
cd /bin
ls
cp ls list
```

When we try to run the list command, instead of getting a list of the contents of the current directory, we will get a Permission denied error message. This is because Aqua considered that new executable to be drift and blocked that executable from running.
```shell
list
```

![drift](/images/runtime_security/drift.png)

Drift prevention ensures that your containers remain immutable, and protects you from both malicious attacks and bad habits by not allowing executables to run that were not part of the original image and/or not allowing the container to run when image parameters have changed.

We can also validate that the ```date``` executable is blocked.

![exec-date](/images/runtime_security/exec-date.png)

Notice that we are now in the nginx container as the root user (something else that can be prevented by Aqua). As such, there’s nothing we should not be able to do. However, if we try executing the date executable, we will get a Permission denied error message. This is because Aqua blocked that executable from running.