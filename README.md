# mikrotik-virtualbox

# Configuración Inicial de MikroTik después de una Instalación Nueva

Este tutorial detalla cómo configurar una dirección IP en un MikroTik recién instalado, solucionar problemas de conectividad y habilitar acceso a internet. Ideal para casos donde WinBox muestra la IP como `0.0.0.0`.

## Requisitos
- **WinBox** instalado en tu computadora.
- Acceso físico o de red al dispositivo MikroTik.

---

## Paso 1: Conectarse a MikroTik desde WinBox por MAC Address
1. Abre **WinBox**.
2. En el campo **Connect To**, selecciona la **MAC Address** del dispositivo de la lista.
3. Introduce el usuario y contraseña:
   - **Usuario**: `admin`
   - **Contraseña**: *(vacío por defecto)*
4. Haz clic en **Connect**.

---

## Paso 2: Configurar una Dirección IP
1. En el menú principal, ve a **IP > Addresses**.
2. Haz clic en el botón **+** para agregar una nueva dirección.
3. Configura los siguientes campos:
   - **Address**: `192.168.1.2/24` *(ajústalo a la red de tu gateway, si es diferente, en mi caso usé 192.168.1.28/24)*.
   - **Interface**: Selecciona la interfaz conectada, por ejemplo, `ether1`.
4. Haz clic en **OK** para guardar.

---

## Paso 3: Configurar la Ruta Predeterminada (Gateway)
1. Ve a **IP > Routes**.
2. Haz clic en el botón **+** para agregar una nueva ruta.
3. Configura los siguientes campos:
   - **Dst. Address**: `0.0.0.0/0`.
   - **Gateway**: La IP de tu gateway, por ejemplo, `192.168.1.1`.
4. Haz clic en **OK** para guardar.

---

## Paso 4: Habilitar el Cliente DHCP (Opcional)
Si prefieres que el MikroTik obtenga automáticamente la configuración de red:
1. Ve a **IP > DHCP Client**.
2. Haz clic en el botón **+**.
3. Selecciona:
   - **Interface**: `ether1` (o la interfaz conectada a tu red).
   - Marca las casillas **Add Default Route** y **Use Peer DNS**.
4. Haz clic en **OK**.

---

## Paso 5: Verificar Conectividad
1. En **New Terminal** o desde WinBox, ejecuta los siguientes comandos:
   - Para comprobar si el gateway responde:
     ```bash
     /ping 192.168.1.1
     ```
   - Para verificar la conectividad con internet:
     ```bash
     /ping 8.8.8.8
     ```
   - Si ambos comandos tienen respuesta, prueba:
     ```bash
     /tool fetch url="https://www.google.com" mode=https
     ```

---

## Paso 6: Guardar Configuración
Para asegurar que la configuración persista tras un reinicio:
1. Guarda una copia del respaldo:
   ```bash
   /system backup save name=initial_config
