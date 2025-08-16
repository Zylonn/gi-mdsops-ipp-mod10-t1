# Ataque AS-REP Roasting en Active Directory

## Descripción
Este proyecto reproduce un ataque de **AS-REP Roasting** en un entorno de Active Directory montado en máquinas virtuales.  
El objetivo es demostrar cómo una configuración insegura en cuentas de usuario puede permitir a un atacante obtener y crackear hashes de Kerberos **sin necesidad de conocer previamente la contraseña**.

---

## Laboratorio
El entorno está compuesto por las siguientes VMs:
- **Windows Server 2019 (DC-Task1)** – Controlador de dominio `Campus-Arq.local`
- **Windows 11 (W11-Task1)** – Cliente unido al dominio
- **Kali Linux** – Máquina atacante con herramientas de enumeración y cracking

---

## Índice de pasos

1. **Preparación en el DC (Windows Server 2019)**
   - Crear un usuario en Active Directory con la opción *“No pedir la autenticación Kerberos previa”*.
   - Asegurarse de que pertenece al dominio `Campus-Arq.local`.

2. **Verificación desde el cliente Windows 11**
   - Confirmar que el usuario creado puede iniciar sesión en el dominio.
   - Comprobar con `klist` que obtiene tickets Kerberos (TGT y TGS).

3. **Enumeración desde Kali Linux**
   - Descubrir usuarios del dominio con `impacket-GetNPUsers`.
   - Identificar el usuario vulnerable sin preautenticación.

4. **Explotación y Cracking**
   - Solicitar un AS-REP del usuario vulnerable.
   - Extraer el hash en formato crackeable.
   - Usar **John the Ripper** con el diccionario `rockyou.txt` para obtener la contraseña en claro.

5. **Demostración de impacto**
   - Autenticarse en el dominio con `smbclient` usando la contraseña crackeada.
   - Listar recursos compartidos del DC (`ADMIN$`, `C$`, `IPC$`, `NETLOGON`, `SYSVOL`).
   - Acceder al recurso `SYSVOL` y comprobar directorios de **Policies** (GPOs).

6. **Mitigación**
   - Revisar que ningún usuario tenga desactivada la preautenticación Kerberos.
   - Aplicar **políticas de contraseñas robustas**.
   - Monitorizar los eventos Kerberos (Event ID 4768).
   - Restringir privilegios y aplicar el principio de mínimo privilegio.

---

## Resultados
- Se creó el usuario vulnerable `victima` en el dominio `Campus-Arq.local`.
- Desde Kali Linux se obtuvo un hash AS-REP del usuario.
- El hash fue crackeado con éxito revelando la contraseña en claro: `Password123!`.
- Con las credenciales crackeadas se accedió a los recursos compartidos del DC, incluyendo `SYSVOL`.

---

## Conclusión
El ataque AS-REP Roasting demostró cómo un error de configuración en Active Directory permite a un atacante:
- Obtener hashes Kerberos sin necesidad de preautenticación.
- Crackear contraseñas de usuario en claro con diccionarios comunes.
- Acceder a recursos compartidos críticos del dominio.

La mitigación más directa consiste en **asegurarse de que todas las cuentas requieran preautenticación Kerberos**, acompañada de controles adicionales como políticas de contraseñas robustas y monitorización de eventos.

---
