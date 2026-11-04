## Configurar Kubernetes

- Un Master

- Un Worker solamente

### previo - Inventario de equipos

**Con linux como hipervisor:**

- Listar mis máquinas: 
  
  ```bash
  virsh list --all
  ```
- Clonar una VM:
  
  ```bash
  virt-clone --original rocky9 --name rocky9-clone --auto-clone
  ```
- Conocer la IP de las VM que se encuentran en ejecución
  
  ```bash
  virsh net-dhcp-leases default
  ```
- Eliminar una VM
  
  ```bash
  virsh undefine rocky9-clone --remove-all-storage --nvram
  ```
  
  ### inventario en mi caso:
  
  Dos rorcy9 con las siguientes IP
  
  ```shellsession
  martin@pc-bdx-mqr:~/tsic-2/tarea-kubernetes$ virsh net-dhcp-leases default
   Expiry Time           MAC address         Protocol   IP address           Hostname        Client ID or DUID
  -----------------------------------------------------------------------------------------------------------------
   2026-11-03 21:10:03   52:54:00:10:23:5f   ipv4       192.168.122.40/24    k8s-master-01   01:52:54:00:10:23:5f
   2026-11-03 20:45:27   52:54:00:d2:e7:ee   ipv4       192.168.122.120/24   -               01:52:54:00:d2:e7:ee
  ```
  
  
  
  ### 1 - Configurar hostnames
  
  *En contra de mi voluntad, trabajaré con root, para no complicar las cosas*
  
  #### 1.1 - En el master
1. Configurar hostname
    de manera inmediata mediante la herramienta
   
   ```bash
   hostamectl set hostname "k8s-master1"
   ```
   
    O también es válido manualmente
    editar **/etc/hostname**
    y colocar
   
   ```confi
   ks8-master1
   ```

2. Agregar identificador DNS para los demás nodos
    **/etc/hostname**
   
   ```conf
   127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
   ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
   127.0.0.1   ks8-master01
   192.168.122.40  ks8-master01
   192.168.122.120 k8s-worker01
   ```
