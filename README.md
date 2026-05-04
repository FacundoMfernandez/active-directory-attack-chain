# Cadena de Ataque sobre Active Directory

> Proyecto final del **Bootcamp 360° en Ciberseguridad** de The Bridge Digital Talent Accelerator (Abril 2026).

## 🎯 Objetivo

Simular la cadena completa de un atacante que compromete un usuario sin privilegios y, paso a paso, escala hasta Domain Admin con persistencia indefinida — para luego diseñar el plan de detección y mitigación desde una perspectiva Blue Team.

## 🧪 Entorno de laboratorio (controlado)

- **Domain Controller**: Windows Server 2019
- **Workstations**: 2 × Windows 10 / Linux 
- **Atacante**: Kali Linux 2024.1
- **Red**: Red NAT aislada (sin acceso a internet desde el dominio)

## 🔁 Fases ejecutadas

### 1. Reconocimiento (Recon)
Enumeración de usuarios, grupos y SPNs con `BloodHound` + `SharpHound`. Identificación de caminos de ataque hacia Domain Admin.

### 2. Movimientos laterales — Pass-the-Hash
Reutilización de hashes NTLM extraídos vía `mimikatz` para autenticarse contra otros sistemas con `Impacket-psexec`.

### 3. Kerberoasting
Extracción de tickets TGS de cuentas con SPN registrado mediante `Impacket-GetUserSPNs`. Crackeo offline con `hashcat` para obtener credenciales privilegiadas.

### 4. Escalada de privilegios
Aprovechamiento de cuenta de servicio comprometida para alcanzar Domain Admin.

### 5. Persistencia — Golden Ticket
Generación de TGT propio con la clave KRBTGT — acceso indefinido al dominio incluso si se rota la contraseña del usuario originalmente comprometido.

## 🛠️ Herramientas utilizadas

| Herramienta | Uso en el proyecto |
|---|---|
| BloodHound + SharpHound | Enumeración y mapeo de relaciones AD |
| mimikatz | Extracción de hashes NTLM y tickets Kerberos |
| Impacket (psexec, GetUserSPNs) | Movimientos laterales y Kerberoasting |
| hashcat | Crackeo offline de tickets TGS |
| Kali Linux | Plataforma de ataque |
| Wireshark | Análisis de tráfico de red durante el ataque |
| Sysmon + Windows Event Logs | Detección Blue Team |

## 🛡️ Detección Blue Team

> **La parte más importante del proyecto.**

Por cada técnica ofensiva ejecutada, documenté qué eventos genera en Windows, qué reglas de SIEM detectarían el patrón, y qué controles defensivos lo mitigan.

### Mapeo MITRE ATT&CK

| Técnica usada | ID ATT&CK | Detección recomendada |
|---|---|---|
| Enumeración con BloodHound | T1087.002 | Sysmon Event ID 10, eventos LDAP query masiva |
| Pass-the-Hash | T1550.002 | Windows Event 4624 logon type 9 + NTLM |
| Kerberoasting | T1558.003 | Event 4769 con tipo encriptación RC4 (0x17) |
| Golden Ticket | T1558.001 | Event 4624 con TGT lifetime anómalo, SID History |

## 📊 Resultado

Cadena completa ejecutada en ~3 horas en entorno controlado. Plan de mitigación con 14 controles concretos priorizados por impacto/esfuerzo.

## 🔒 Nota de confidencialidad

> **⚠️ Los informes técnicos detallados, capturas de pantalla y evidencias del proyecto no se publican en este repositorio por razones de confidencialidad con la entidad formativa (The Bridge Digital Talent Accelerator).**
>
> **Si eres reclutador o profesional del sector y deseas ver el material completo (informes técnicos, PoC, capturas y planes de mitigación), no dudes en contactarme:**
>
> 📧 **seguridadinformaticamf@gmail.com**
> 🔗 **[LinkedIn](https://www.linkedin.com/in/facundo-martin-fernandez-oton-/)**
>
> Estaré encantado de compartirlo en una conversación directa o entrevista técnica.

## ⚠️ Disclaimer

Este proyecto se ejecutó en un entorno de laboratorio aislado y controlado. Las técnicas documentadas son educativas y están orientadas a la mejora de capacidades defensivas (Blue Team). El uso no autorizado de estas técnicas es ilegal.

## 📧 Contacto

[Facundo Martín Fernández Otón](https://www.linkedin.com/in/facundo-martin-fernandez-oton-/) · [GitHub](https://github.com/FacundoMfernandez)

---

🔍 *¿Estás reclutando para un equipo SOC, Blue Team o IR? Mirá mi [perfil](https://github.com/FacundoMfernandez) y hablemos.*
