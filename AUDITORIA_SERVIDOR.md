# Auditoría de Servidor WHM/cPanel en AWS

## Información del Servidor
- **IP**: 52.204.255.250
- **Hostname**: server3.eclypse-cloud.com
- **Plataforma**: AWS EC2
- **Panel**: WHM/cPanel 138.0 (build 21)
- **SO**: CloudLinux release 9.7 (Pavel Popovich)

## Resumen de Componentes

### Servidor Web
- **Tipo**: LiteSpeed Web Server
- **Versión**: 6.3.4
- **Estado**: ❌ CAÍDO desde 14/11/2026 00:02 (13+ horas)

### Base de Datos
- **Tipo**: MariaDB
- **Versión**: 10.11.14
- **Puerto**: 3306 (solo localhost)
- **Estado**: ✅ Corriendo

### PHP (EasyApache 4)
| Versión | Build Date | Estado |
|---------|------------|--------|
| PHP 8.1.34 | Oct 21 2026 | ✅ |
| PHP 8.2.35 | Oct 22 2026 | ✅ |
| PHP 8.3.36 | Oct 22 2026 | ✅ |
| PHP 8.4.27 | Oct 22 2026 | ✅ |

### Recursos del Sistema
- **RAM**: 30GB total, ~776MB libre, 15GB disponibles
- **Swap**: No configurado ⚠️
- **Disco**: 63% usado (240GB libres de 639GB)
- **Uptime**: 18 días
- **Load Average**: 0.89

### Cuentas de Hosting (sitios)
- aridos, armyofmaids, begood, cosentino, dgbabogados, eclypse, eclypsecloud, eclypsedsg, elyar, y otros...

## Hallazgos de Seguridad

### 🔴 CRÍTICO - Servicios Caídos

1. **LiteSpeed HTTP Server (lshttpd)** - CAÍDO
   - Estado: `failed (Result: timeout) since Sat 2026-11-14 00:02:06`
   - Múltiples intentos de reinicio fallidos
   - Impacto: **Sitios web no disponibles**

2. **cPhulkd (Protección Fuerza Bruta)** - CAÍDO
   - Estado: `failed (Result: protocol) since Fri 2026-11-06 13:11:25`
   - Duración: **1 semana abajo**
   - Impacto: **Vulnerable a ataques de fuerza bruta**

3. **Imunify360** - Estado不确定
   - Paquetes instalados pero agente no responde correctamente

### 🟡 CONFIGURACIÓN DE ACTUALIZACIONES

Ubicación: `/etc/cpupdate.conf`
```
CPANEL=11.138.0.24
RPMUP=manual
SARULESUP=manual
SECURITY_UPDATES=hourly
STAGING_DIR=/usr/local/cpanel
UPDATES=manual
```
- cPanel actualizado manualmente (no automático)
- Actualizaciones de seguridad: Habilitadas (hourly)

### 🟢.positivo

- MySQL solo escucha en localhost (bind-address 127.0.0.1)
- AWS Security Group activo (firewall a nivel de nube)
- Certificados SSL configurados (cPanel, Exim, Dovecot, FTP)

## Acciones Pendientes

### 1. ✅ LiteSpeed - REINICIADO (14/11/2026 13:43)
- Estado: **Activo y corriendo**
- Nota: Muestra página por defecto - revisar virtual hosts

### 2. ⚠️ cPhulkd - FALLO AL REINICIAR
- Error: "The cphulkd service is not configured"
- Requiere revisión manual de configuración de cPanel

### 3. ✅ Imunify360 - CORRIENDO
- Estado: Activo desde 11/11/2026
- Proveedor de protección de seguridad

### 4. Configurar Swap (Pendiente)
```bash
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### 5. Revisar Virtual Hosts (Pendiente)
- LiteSpeed muestra página por defecto
- Verificar configuración de dominios en WHM

## Notas de la Auditoría

- Fecha: 14/11/2026
- Auditor: Developer (Agente IA)
- Las actualizaciones de PHP están al día (builds de Oct 2026)
- Las versiones de PHP 8.1, 8.2, 8.3 y 8.4 están instaladas
- cPanel version 138.0 disponible,installed 138.0 build 21

## Siguiente Sesión

1. Revisar este documento primero
2. Confirmar si LiteSpeed fue reiniciado
3. Verificar estado de cPhulkd
4. Ejecutardnf update --security para ver actualizaciones pendientes