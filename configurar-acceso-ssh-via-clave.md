## Configurar Acceso SSH via clave

Tomado del manual del profesor, material fúsilado y publicado en

[Ansible → Kubernetes · DevOps 2026](https://mquinteror.net/tsic/kubernetes/)

### Inventario de equipos ( debian13 )

```shell
root@pc-bda-mqr:~# virsh net-dhcp-leases default
 Expiry Time           MAC address         Protocol   IP address           Hostname       Client ID or DUID
----------------------------------------------------------------------------------------------------------------
 2026-11-05 10:23:12   52:54:00:45:59:af   ipv4       192.168.122.215/24   k8s-master01   01:52:54:00:45:59:af
 2026-11-05 10:22:52   52:54:00:c4:4e:29   ipv4       192.168.122.196/24   k8s-worker01   01:52:54:00:c4:4e:29
```

#### Agregar registro dns a los equipos

Con esto no será necesario recordar la IP de los equipos

editar **/etc/hosts** y agregar las siguientes entradas

```bash
## agregados para tsic
# rocky que sera master
192.168.122.215 k8s-master01 master01
# rocky que sera worker
192.168.122.196 k8s-worker01 worker01
```

después de esto, tanto k8s-master, cómo master01 resuelven a 192.168.122.215. Lo mismo para worker01

probar

## En la máquinas física ( debian13 )

crear una identidad ssh

```bash
ssh-keygen -t ed25519 -C "root@control"
```

##### Agregar identidad en master (192.168.122.215)

usar ssh-copy-id para agregar la identidad en master01

```bash
ssh-copy-id root@worker01
```

será esta la última vez que solicite la contraseña al conectarse a master01

```bash
ssh master01
```

ahora deja pasar directo a **master01**

##### Agregar identidad en worker01 (192.168.122.196)

usar ssh-copy-id para agregar la identidad en master01

```bash
ssh-copy-id root@master01
```

será esta la última vez que solicite la contraseña al conectarse a worker01

```bash
ssh worker01
```

ahora deja pasar directo a **worker01**

# 
