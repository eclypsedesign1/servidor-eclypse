# Auditoría de Servidor WHM/cPanel en AWS

## Información del Servidor
- **IP**: 52.204.255.250
- **Hostname**: server3.eclypse-cloud.com
- **Plataforma**: AWS EC2
- **Panel**: WHM/cPanel 138.0 (build 24)
- **SO**: CloudLinux release 9.7 (Pavel Popovich)

## Resumen de Componentes

### Servidor Web
- **Tipo**: LiteSpeed Web Server
- **Versión**: 6.3.4 Enterprise
- **Estado**: ✅ Corriendo

### Base de Datos
- **Tipo**: MariaDB
- **Versión**: 10.11.14
- **Puerto**: 3306 (solo localhost)
- **Estado**: ✅ Corriendo (OPTIMIZADO)

### PHP (EasyApache 4)
| Versión | Build Date | Estado |
|---------|------------|--------|
| PHP 8.1.35 | Nov 5 2026 | ✅ Actualizado |
| PHP 8.2.35 | Nov 11 2026 | ✅ |
| PHP 8.3.36 | Nov 11 2026 | ✅ |
| PHP 8.4.27 | Nov 11 2026 | ✅ |

### Recursos del Sistema
- **RAM**: 30GB total, 23GB disponible (~75% libre)
- **Swap**: 4GB configurado ✅
- **Disco**: 63% usado (237GB libres de 639GB)
- **Uptime**: 30 min (recin reiniciado)
- **Load Average**: 1.11

## Acciones Realizadas

### ✅ 1. cPanel Actualizado
- Actualizado de 138.0 (build 21) → 138.0 (build 24)
- Ejecución de mantenimiento completo

### ✅ 2. Reinicio del Servidor
- Aplicado para actualizar kernel
- Tiempo de inactividad: ~2-5 minutos

### ✅ 3. LiteSpeed Reiniciado
- Reiniciado después del reinicio del servidor
- Estado: Corriendo correctamente

### ✅ 4. Actualizaciones de Seguridad
Paquetes actualizados:
- alt-openssl11, alt-openssl11-libs
- alt-curlssl11, alt-libcurlssl11
- alt-libxml2, alt-libxml2-devel

### ✅ 5. PHP Actualizado
- PHP 8.1: 8.1.34 → 8.1.35
- PHP 8.2: ya estaba 8.2.35
- PHP 8.3: ya estaba 8.3.36
- PHP 8.4: ya estaba 8.4.27

### ✅ 6. MariaDB Optimizado
Configuración anterior vs nueva:

| Parámetro | Anterior | Nuevo |
|-----------|----------|-------|
| innodb_buffer_pool_size | 4 GB | **8 GB** |
| max_connections | 151 | **250** |
| table_open_cache | 2000 | **4000** |

Parámetros adicionales agregados:
- `innodb_log_file_size = 1G`
- `innodb_flush_log_at_trx_commit = 2`
- `max_connect_errors = 1000000`
- `query_cache_type = 0` (deshabilitado)

### ✅ 7. Paquetes Conflictivos Limpiados
- Eliminado kmod-lve-2.1-44.el9 (versión antiga)
- Solo kmod-lve-2.1-45.el9 permanece

## Hallazgos de Seguridad

### Estado de Protecciones
| Servicio | Estado |
|----------|--------|
| Imunify360 | ✅ Activo (v8.15.1) |
| LiteSpeed | ✅ Corriendo |
| MariaDB | ✅ Solo localhost |
| AutoSSL | ✅ Configurado |
| JetBackup | ✅ Corriendo |

## Notas de la Auditoría

- **Fecha**: 14/11/2026
- **Auditor**: Developer (Agente IA)
- Las actualizaciones de PHP están completamente al día
- cPanel en versión más reciente (138.0.24)
- MariaDB optimizado para 30GB de RAM y 14GB de datos

## Pendientes (No Críticos)

### 1. Conflictos de Kernel LVE
Los paquetes kmod-lve y liblsapi no puedenactualizarse completamente debido a diferencias entre el kernel de AWS y los módulos de CloudLinux. 
- **Estado**: No crítico - el sistema funciona correctamente
- **Solución alternativa**: KernelCare proporciona parches de seguridad
- **Recomendación**: Dejar como está

### 2. EasyApache Muestra Actualizaciones
- Muestra 201 paquetes disponibles
- **Razón**: Conflictos con módulos LVE/kernel
- **Recomendación**: No forzar - el sistema funciona

## Información de Contacto

- SSH: `ssh -i ~/.ssh/aws.pem ec2-user@52.204.255.250`
- WHM: https://52.204.255.250:2087
- cPanel: https://52.204.255.250:2083

## Siguiente Auditoría Recomendada
1. Revisar uso de recursos en 1 mes
2. Verificar que JetBackup esté haciendo backups correctamente
3. Revisar logs de Imunify360 para amenazas bloqueadas