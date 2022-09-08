# freshrss-kubernetes

A project to deploy [FreshRSS](https://freshrss.org/) in a scalable and secure way on Kubernetes

Features:
- Automatic creation of initial SuperUser
- Secure setup with no root in any container and read-only root filesystem
- Seperated static file serving using NGINX

## Adding the repo
The helm repo can be added by running
```
helm repo add iloveyatoo https://helm.catsdo.delivery/
```
