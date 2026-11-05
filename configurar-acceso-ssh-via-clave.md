## Configurar Acceso SSH via clave

Tomado del manual del profesor, material fúsilado y publicado en

[Ansible → Kubernetes · DevOps 2026](https://mquinteror.net/tsic/kubernetes/)

### Inventario de equipos

```shell
martin@pc-bdx-mqr:~/tsic-2/tarea-kubernetes$ virsh net-dhcp-leases default
 Expiry Time           MAC address         Protocol   IP address           Hostname        Client ID or DUID
-----------------------------------------------------------------------------------------------------------------
 2026-11-03 21:10:03   52:54:00:10:23:5f   ipv4       192.168.122.40/24    k8s-master-01   01:52:54:00:10:23:5f
 2026-11-03 20:45:27   52:54:00:d2:e7:ee   ipv4       192.168.122.120/24   -               01:52:54:00:d2:e7:ee
```

diagrama de merm
