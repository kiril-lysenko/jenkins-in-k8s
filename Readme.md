Шаг 1
```shell
dpkg -l | grep -i docker
```
Чтобы определить, какой установленный пакет установлен:

Шаг 2
```shell
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-compose-plugin
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-compose-plugin
```
Команды выше не удалят образы, контейнеры, тома или файлы конфигурации, созданные пользователем, на вашем хосте. Если надо удалить все образы, контейнеры и тома, выполните следующие команды:
```shell
sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
sudo rm -rf /var/lib/containerd
```
Вы полностью удалили Docker из системы.

Просто пустая конфигурация с исправленной проблемой systemd_cgroup
```shell
cat > /etc/containerd/config.toml <<EOF
[plugins."io.containerd.grpc.v1.cri"]
  systemd_cgroup = true
EOF
```
```shell
systemctl restart containerd
```
