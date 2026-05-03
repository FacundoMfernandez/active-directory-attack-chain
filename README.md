# active-directory-attack-chain
Cadena de ataque completa sobre Active Directory: enumeración, Pass-the-Hash, Kerberoasting, Golden Ticket. Proyecto final del Bootcamp 360° The Bridge
# Cadena de Ataque sobre Active Directory

> Proyecto final del **Bootcamp 360° en Ciberseguridad** de The Bridge Digital Talent Accelerator (Abril 2026).

## 🎯 Objetivo

Simular la cadena completa de un atacante que compromete un usuario sin privilegios y, paso a paso, escala hasta Domain Admin con persistencia indefinida — para luego diseñar el plan de detección y mitigación desde una perspectiva Blue Team.

## 🧪 Entorno de laboratorio (controlado)

- **Domain Controller**: Windows Server 2019, dominio `lab.local`
- **Workstations**: 2 × Windows 10
- **Atacante**: Kali Linux 2024.1
- **Red**: VirtualBox / VMware NAT aislada (sin acceso a internet desde el dominio)

## 🔁 Fases ejecutadas

### 1. Reconocimiento (Recon)
Enumeración de usuarios, grupos y SPNs con `BloodHound` + `SharpHound`. Identificación de caminos de ataque hacia Domain Admin.

📄 [Detalle: 02-recon-enumeration.md](./docs/02-recon-enumeration.md)

### 2. Movimientos laterales — Pass-the-Hash
Reutilización de hashes NTLM extraídos vía `mimikatz` para autenticarse contra otros sistemas con `Impacket-psexec`.

📄 [Detalle: 03-lateral-movement.md](./docs/03-lateral-movement.md)

### 3. Kerberoasting
Extracción de tickets TGS de cuentas con SPN registrado mediante `Impacket-GetUserSPNs`. Crackeo offline con `hashcat` para obtener credenciales privilegiadas.

📄 [Detalle: 04-kerberoasting.md](./docs/04-kerberoasting.md)

### 4. Escalada de privilegios
Aprovechamiento de cuenta de servicio comprometida para alcanzar Domain Admin.

📄 [Detalle: 05-privilege-escalation.md](./docs/05-privilege-escalation.md)

### 5. Persistencia — Golden Ticket
Generación de TGT propio con la clave KRBTGT — acceso indefinido al dominio incluso si se rota la contraseña del usuario originalmente comprometido.

📄 [Detalle: 06-persistence.md](./docs/06-persistence.md)

## 🛡️ Detección Blue Team

> **La parte más importante del proyecto.**

Por cada técnica ofensiva ejecutada, documenté qué eventos genera en Windows, qué reglas de SIEM detectarían el patrón, y qué controles defensivos lo mitigan.

📄 [Plan de detección completo: 07-blue-team-detection.md](./docs/07-blue-team-detection.md)

### Mapeo MITRE ATT&CK

| Técnica usada | ID ATT&CK | Detección recomendada |
|---|---|---|
| Enumeración con BloodHound | T1087.002 | Sysmon Event ID 10, eventos LDAP query masiva |
| Pass-the-Hash | T1550.002 | Windows Event 4624 logon type 9 + NTLM |
| Kerberoasting | T1558.003 | Event 4769 con tipo encriptación RC4 (0x17) |
| Golden Ticket | T1558.001 | Event 4624 con TGT lifetime anómalo, SID History |

## 📊 Resultado

Cadena completa ejecutada en ~3 horas en entorno controlado. Plan de mitigación entregado al equipo formativo con 14 controles concretos priorizados por impacto/esfuerzo.

## 📑 Informes entregables

- 📄 [Informe técnico (PDF)](./reports/informe-tecnico.pdf) — reproducible paso a paso
- 📄 [Informe ejecutivo (PDF)](./reports/informe-ejecutivo.pdf) — orientado a dirección, sin jerga

## 📂 Estructura del repositorio

```
active-directory-attack-chain/
├── README.md
├── LICENSE
├── docs/
│   ├── 01-environment-setup.md
│   ├── 02-recon-enumeration.md
│   ├── 03-lateral-movement.md
│   ├── 04-kerberoasting.md
│   ├── 05-privilege-escalation.md
│   ├── 06-persistence.md
│   ├── 07-blue-team-detection.md
│   └── 08-mitigation-plan.md
├── screenshots/
├── scripts/
└── reports/
    ├── informe-tecnico.pdf
    └── informe-ejecutivo.pdf
```

## ⚠️ Disclaimer

Este proyecto se ejecutó en un entorno de laboratorio aislado, propiedad del autor y sin acceso a sistemas de terceros. Las técnicas documentadas son educativas y están orientadas a la mejora de capacidades defensivas (Blue Team). El uso no autorizado de estas técnicas es ilegal.

## 📧 Contacto

[Facundo Martín Fernández Otón](https://www.linkedin.com/in/facundo-martin-fernandez-oton-/)

---

🔍 *¿Estás reclutando para un equipo SOC, Blue Team o IR? Mirá mi [perfil](https://github.com/FacundoMfernandez) y hablemos.*
