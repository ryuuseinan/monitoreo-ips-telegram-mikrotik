# Monitoreo de IPs de firewall de dispositivo Mikrotik con integración de Bot de Telegram

## Configuración Inicial de MikroTik después de una Instalación Nueva

Este tutorial detalla cómo configurar una dirección IP en un MikroTik recién instalado, solucionar problemas de conectividad y habilitar acceso a internet. Ideal para casos donde WinBox muestra la IP como `0.0.0.0`.

## Requisitos
- **WinBox** instalado en tu computadora.
- Acceso físico de red al dispositivo MikroTik o tener instalado una máquina virtual.
- En caso de contar con una máquina virtual, haber instalado RouterOS en esta.
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

# Enviar Mensajes a Telegram desde MikroTik

Este proyecto describe cómo configurar un script en MikroTik para enviar mensajes a un chat de Telegram utilizando la API de Telegram.

## Requisitos

- Un router MikroTik con acceso a la terminal o Winbox.
- Un bot de Telegram creado con [BotFather](https://core.telegram.org/bots#botfather).
- El `BotToken` y el `ChatID` del chat al que se enviarán los mensajes.

## Pasos para configurar el script

### 1. Crear un bot de Telegram
1. Abre Telegram y busca el usuario `@BotFather`.
2. Inicia una conversación con él y usa el comando `/newbot` para crear un nuevo bot.
3. Anota el `BotToken` que te proporciona BotFather, ya que lo necesitarás en el script.

### 2. Obtener el `ChatID`
Para obtener el `ChatID` de tu chat en Telegram, sigue estos pasos:
1. En Telegram, abre el siguiente enlace en tu navegador (reemplaza `BotToken` por el que te dio BotFather): https://api.telegram.org/bot<BotToken>/getUpdates
2. Esto devolverá una respuesta JSON con información sobre los mensajes enviados al bot. Busca el campo `"chat": {"id": <ChatID>}` y copia el `ChatID`.

### 3. Configurar el script en MikroTik
1. Accede a la terminal de MikroTik, ya sea mediante Winbox o SSH.
2. Crea un nuevo script con el siguiente comando:

```bash
/system script add name=script source={
  :local BotToken ""
  :local ChatID ""
  :local Message "Mensaje de prueba desde MikroTik"
  /tool fetch url=("https://api.telegram.org/bot" . $BotToken . "/sendMessage%3Fchat_id=" . $ChatID . "&text=" . $Message) keep-result=no
}
```
### 4. Ejecutar el script
1. Para ejecutar el script simplemente abre una terminal y escribe `/system script run script`
2. Para probar la solución se deben eliminar o agregar IPs.
- Para agregar abre una terminar y escribe `/ip firewall address-list add list=BlackList address=192.168.1.15`
- Para eliminar `/ip firewall address-list remove [find list=BlackList address=192.168.1.15]`
3. Una vez enviado uno de estos dos comandos, llegará una notificación a Telegram con el mensaje correspondiente.
