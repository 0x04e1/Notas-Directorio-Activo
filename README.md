# Notas.
[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

# Tabla de contenido

- [Enumeración](#Enumeración).
- [*Logon Sesssion*](#LogonSession).
- [*Token*](#Token).

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
# *LogonSession*

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

# Módulos a importar para la enumeración.

# ADModule:
```powershell
Import-Module .\ADModule-master\Microsoft.ActiveDirectory.Management.dll
Import-Module .\ADModule-master\ActiveDirectory\ActiveDirectory.psd1
```
# PowerView:
```powershell
Import-Module .\PowerView.ps1
```
