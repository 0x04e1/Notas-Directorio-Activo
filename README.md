## Tabla de contenido

- [ENUMERACIÓN](#Enumeración).
- [Cursivas](#cursivas).
- [Negrilla](#negrilla).
- [Viñetas para tablas de contenido](#vinetas).
- [insertar imágenes](#insertar-imagenes).
- [Insertar enlaces](#insertar-enlaces).
- [Hacer anclaje](#hacer-anclaje).
- [Insertar una línea de código](#insertar-una-linea-de-codigo).
- [Insertar un bloque de código](#insertar-un-bloque-de-codigo).
- [Resaltar el código](#resaltar-el-codigo).
- [Insertar tablas](#insertar-tablas).
- [Otras referencias sobre Markdown](#otras-referencias-sobre-markdown).

# ENUMERACIÓN
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
