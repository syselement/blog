# MobSF 

> 🔗 [Mobile-Security-Framework-MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
>
> 🔗 [MobSF - Docker](https://mobsf.github.io/docs/#/docker)

## Install MobSF - Docker

- Install MobSF with Persistence via Docker

```bash
docker pull opensecurity/mobile-security-framework-mobsf
mkdir -p ~/docker/mobsf
chown 9901:9901 ~/docker/mobsf
docker run -it --rm --name mobsf -p 8000:8000 -v ~/docker/mobsf:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest
```

- Create bash/zsh Alias

```bash
mobsf='docker run -it --rm --name mobsf -p 8000:8000 -v ~/docker/mobsf:/home/mobsf/.MobSF opensecurity/mobile-security-framework-mobsf:latest'
```

---

