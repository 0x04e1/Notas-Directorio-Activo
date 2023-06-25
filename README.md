# Notas.
[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

# Tabla de contenido

- [Enumeración](#Enumeración).
- [Logon Sesssion](#LogonSession).

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
# Logon Session

![Token](https://github.com/0x04e1/Notas-Directorio-Activo/blob/main/Pic/1.png)

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
