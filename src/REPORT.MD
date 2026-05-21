## Этап 1. Создание трёх виртуальных машин







> Для задания нужны именно ВМ, а не Docker-контейнеры. Самый простой способ на Ubuntu — Multipass (это утилита от Canonical, она создаёт лёгкие Ubuntu-ВМ через KVM в пару команд).

> Multipass по умолчанию использует QEMU/KVM на Linux — это значит, что ВМ будут запускаться через аппаратную виртуализацию, без оверхеда (overhead — накладные расходы) на эмуляцию.

![alt text](img/1/image.png)




**Создаём три ВМ**

![alt text](<img/1/image copy.png>)

**# Мастер-узел: 2 ядра, 2 ГБ ОЗУ, 15 ГБ диска**

**# Первый воркер**

**# Второй воркер**





![alt text](<img/1/image copy 2.png>)

**Проверка, что все три ВМ поднялись**


```
s@Vivo:~/DO-11$ MASTER_IP=$(multipass info k3s-master | grep IPv4 | awk '{print $2}')
echo $MASTER_IP
10.174.8.95
```


```
ubuntu@k3s-master:~$ curl -sfL https://get.k3s.io | sh -s - --disable=traefik --write-kubeconfig-mode 644
[INFO]  Finding release for channel stable
[INFO]  Using v1.35.4+k3s1 as release
[INFO]  Downloading hash https://github.com/k3s-io/k3s/releases/download/v1.35.4%2Bk3s1/sha256sum-amd64.txt
[INFO]  Downloading binary https://github.com/k3s-io/k3s/releases/download/v1.35.4%2Bk3s1/k3s
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
[INFO]  Skipping installation of SELinux RPM
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Creating /usr/local/bin/ctr symlink to k3s
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink /etc/systemd/system/multi-user.target.wants/k3s.service → /etc/systemd/system/k3s.service.
[INFO]  systemd: Starting k3s
ubuntu@k3s-master:~$ sudo k3s kubectl get nodes
NAME         STATUS   ROLES           AGE   VERSION
k3s-master   Ready    control-plane   16s   v1.35.4+k3s1
ubuntu@k3s-master:~$ sudo cat /var/lib/rancher/k3s/server/node-token
K102a0844657ffcb3fa8e62ae92a742a9124d6b274346696ea625e70efa10ee391a::server:0d0768b3a3f9e758aec953c04b3e26d9
ubuntu@k3s-master:~$ exit
```


Заходим на первый воркер:
Запускаем установку k3s в режиме агента (воркера). Подставил  IP мастера и токен:

```
curl -sfL https://get.k3s.io | K3S_URL=https://10.174.8.95:6443 K3S_TOKEN=K102a0844657ffcb3fa8e62ae92a742a9124d6b274346696ea625e70efa10ee391a::server:0d0768b3a3f9e758aec953c04b3e26d9 sh -
```




## Этап 2. Установка k3s



![alt text](<img/1/image copy 3.png>)

**Заходим в ВМ мастера:**









<br>
<br>
<br>















##    1. Установка k3s

1) Отчет содержит список команд для установки дистрибутива Kubernetes k3s на трех удаленных виртуальных машинах.
2) Обратите внимание, что Kubernetes не содержит стандартный контроллер Ingress Traefik.































<br>
<br>
<br>


## Part 1. Развертывание собственного кластера k3s

### Задание

1. Получить набор виртуальных машин для кластера.

2. Установить k3s на всех трех машинах. При установке не использовать стандартный Ingress Controller при помощи флага `--disable=traefik`.

3. Выполнить подключение узлов к кластеру, используя команду `k3s server` и флаги `--token` и `--server` для рабочих узлов и мастера соответственно. Когда k3s установлен, можно использовать переменную окружения `NODE_TOKEN`.

4. Установить Ingress Controller Nginx вместо стандартного. Ты можешь использовать официальный файл манифеста Ingress контроллера на базе Nginx, доступный на GitHub.

5. Получить доменное имя и сконфигурировать внутри кластера утилиту `cert-manager`, которая должна генерировать wildcard-сертификат для полученного домена.

6. Создать ресурс Ingress для своего личного домена и настроить его для использования контроллера nginx ingress и полученного сертификата.

7. Создать PV (Persistent Volume) для базы данных PostgreSQL в манифесте из десятого проекта.

8. Запустить приложение, описанное в манифесте.

9. Запустить функциональные тесты Postman и удостовериться в работоспособности приложения.

10. Установить и запустить Prometheus Operator для сбора метрик в системе. Продемонстрировать в отчете результат выполнения команды `kubectl get pods -n monitoring`.
