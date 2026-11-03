## Configurar Kubernetes
### previo - Inventario de equipos
**Con linux como hipervisor:**
- Listar mis máquinas: 
- Clonar una VM:
- Conocer la IP de una VM:

### 1 - configurar hostnames
**En el master**
1. Configurar hostname
    de manera inmediata mediante herramienta
    ```bash
    hostamectl set hostname "k8s-master1"
    ```
    o tambien es válido manualmente
    editar **/etc/hostname**
    y colocar
    ```confi
    ks8-master1
    ```
2. Agregar identificador DNS para los demás nodos
    **/etc/hostname**
    ```conf
    127.0.0.1 k8s-master1
    192.168.122.40 k8s-master1

    ```
