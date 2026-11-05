## Configurar Kubernetes

- Un Master

- Un Worker solamente

#### Requisito. Haber configurado acceso ssh via clave entre los nodos

[configurar-ssh-via-clave]()

### previo - Inventario de equipos

**Con linux como hipervisor:**

- Listar mis máquinas: 
  
  ```bash
  virsh list --all
  ```

- Clonar una VM:
  
  ```bash
  virt-clone --original rocky9-base --name rocky9-clone --auto-clone
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
  root@pc-bda-mqr:~# virsh net-dhcp-leases default 
   Expiry Time           MAC address         Protocol   IP address           Hostname       Client ID or DUID
  ----------------------------------------------------------------------------------------------------------------
   2026-11-05 01:14:18   52:54:00:45:59:af   ipv4       192.168.122.215/24   k8s-master01   01:52:54:00:45:59:af
   2026-11-05 01:14:22   52:54:00:c4:4e:29   ipv4       192.168.122.196/24   k8s-worker01   01:52:54:00:c4:4e:29
  ```

### 1 - Configurar hostnames

  *En contra de mi voluntad, trabajaré con root, para no complicar las cosas*

#### 1.1 - En el master ( 192.168.122.215 )

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
   
   Las importantes son las últimas tres entradas solamente
   
   #### 1.2 - En el worker 1 (192.168.122.196)

3. Configurar hostname
    de manera inmediata mediante la herramienta
   
   ```bash
   hostamectl set hostname "k8s-worker1"
   ```
   
    O también es válido manualmente
    editar **/etc/hostname**
    y colocar
   
   ```confi
   ks8-master1
   ```

4. Agregar identificador DNS para los demás nodos
    **/etc/hostname**
   
   ```conf
   127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
   ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
   127.0.0.1 k8s-worker01
   192.168.122.215 k8s-master01
   192.168.122.196 k8s-worker01
   ```

### 2 - Deshabilitar swap en cada nodo

apagando la swap

```bash
swapoff -a
```

deshabiliatar la entrada de swap en **/etc/fstab**

se encontraba así 

```bash
/dev/mapper/rl-root     /                       ext4    defaults        1 1
UUID=4c93ab3b-2392-4fb7-a0fd-ac0d4f865e1a /boot                   xfs     defaults        0 0
/dev/mapper/rl-home     /home                   ext4    defaults        1 2
/dev/mapper/rl-swap     none                    swap    defaults        0 0
```

Se elimina la entrada de swap, quedando así

```bash
/dev/mapper/rl-root     /                       ext4    defaults        1 1
UUID=4c93ab3b-2392-4fb7-a0fd-ac0d4f865e1a /boot                   xfs     defaults        0 0
/dev/mapper/rl-home     /home                   ext4    defaults        1 2
```

verificar que swap está en cero

```bash
free
```

### 3 - Ajustar SELlinux y las reglas de firewall

##### 3.1 - Deshabilitar selinux

de manera inmediata

```bash
setenforce 0
```

y de manera permanente editando **/etc/sysconfig/selinux**

orignalmente en modo enforcing

```bash
#    grubby --update-kernel ALL --remove-args selinux
#
SELINUX=enforcing
# SELINUXTYPE= can take one of these three values:
#     targeted - Targeted processes are protected,
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

para quedar así

```bash
SELINUX=permissive
# SELINUXTYPE= can take one of these three values:
#     targeted - Targeted processes are protected,
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

comprobar

```bash
getenforce
```

debe salir: permisive

##### 3.2 - Abrir los puertos en master01 ( 192.168.122.215 )

conectarse a master01

```bash
ssh master01
```

Abrir los siguientes puertos con firewall-cmd

```bash
firewall-cmd --permanent --add-port={6443,2379,2380,10250,10251,10252,10259,179}/tcp
firewall-cmd --permanent --add-port=4789/udp
firewall-cmd --reload
```

##### 3.3 - Abrir puertos en worker01 ( 192.168.122.196 )
