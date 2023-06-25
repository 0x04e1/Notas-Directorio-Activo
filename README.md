# Tabla de contenido

- [Enumeración](#Enumeración).

### Enumeración
--Listar usuarios locales:
Get-LocalUser
Get-WmiObject -Class win32_userprofile | select localpath, SID

--Listar usuarios del dominio:
Get-ADUser -Filter *
Get-ADUser -Filter * -Server 192.168.1.155

--Listar grupos locales
Get-LocalGroup

--Listar grupos del dominio
Get-ADGroup -Filter *
Get-ADGroup -Filter * -Server 192.168.1.155

						-> Crea Logon Session
[Usuario] -> [Host] -> 									-> Se crea el Token
						-> Se envía credenciales al LSA
      
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
