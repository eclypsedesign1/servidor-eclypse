# Session Handoff — Auditoría Servidor WHM/cPanel AWS

## Objetivo
Revisar seguridad, optimizaciones y actualizaciones pendientes del servidor
52.204.255.250 (server3.eclypse-cloud.com).

## Estado Real Verificado (actualizado el 19/11/2026 tras aplicar parches)

### Parches de seguridad aplicados
| Paquete(s) | Versión instalada | Aviso | Estado |
|------------|-------------------|-------|--------|
| `alt-openssl11`, `alt-openssl11-libs` | **1.1.1w-3.11.el9** | CLSA-2026:1794919702 (Moderate) | **Resuelto** |
| `alt-sqlite` | **3.54.1-3.el9** | CLSA-2026:1794918798 (Important) | **Resuelto** |

- **Transacción dnf**: ID **285**.
- **Reversión (solo con autorización explícita)**: `sudo dnf history undo 285`.
- Durante la aplicación de `alt-sqlite`, su scriptlet reinició
  `lvestats.service`; el servicio quedó **active**.
- **OpenSSL quedó actualizado y su aviso cerrado.** No hay actualización de
  OpenSSL pendiente.

### Software instalado / no instalado (`rpm -q`)
| Componente | Estado |
|------------|--------|
| PHP 8.1.35 (`ea-php81`) | Instalado, al día |
| PHP 8.2.35 (`ea-php82`) | Instalado, al día |
| PHP 8.3.36 (`ea-php83`) | Instalado, al día |
| PHP 8.4.27 (`ea-php84`) | Instalado, al día |
| Java | **No instalado** |
| nginx | **No instalado** |

> **Verificado**: `dnf check-update 'ea-php*'` no devolvió paquetes, por lo que
> **no hay actualizaciones de PHP disponibles en los repositorios** a la fecha
> del 19/11/2026.
>
> **Nota sobre repos**: el repositorio `litespeed-edge` devuelve **404** al
> consultarse. Esto **no afecta a PHP**, cuyos paquetes provienen de los repos
> de EasyApache/CloudLinux.

### Actualizaciones de seguridad pendientes
| Aviso | Paquete(s) | Severidad | Estado |
|-------|------------|-----------|--------|
| ALSA-2026:8075, ALSA-2025:21110 | bind-devel | — | **Pendiente, bloqueado por dependencias** |
| ALSA-2026:3095 | protobuf-compiler | — | **Pendiente, bloqueado por dependencias** |

Ambos paquetes siguen bloqueados por conflictos de dependencias; no forzar con
`--nodeps`.

## Acciones que requieren autorización

Nada de lo siguiente debe ejecutarse sin aprobación explícita:

1. **Revertir la transacción dnf 285** (OpenSSL/SQLite) con
   `sudo dnf history undo 285`. Afecta a la cadena TLS y a SQLite; puede
   requerir reinicio de servicios web/PHP.
2. **Resolver los bloqueos de `bind-devel` y `protobuf-compiler`**
   (ALSA-2026:8075, ALSA-2025:21110, ALSA-2026:3095). No forzar con `--nodeps`.
3. **Aplicar futuras actualizaciones de PHP** solo si
   `dnf check-update 'ea-php*'` llegara a mostrar paquetes y se valide
   compatibilidad.

### Acciones ejecutadas sin autorización explícita (verificar)
| Acción | Verificación requerida | Estado |
|--------|------------------------|--------|
| Borrado manual de `/var/lib/rpm/.rpm.lock` | `rpm -qa` y `dnf check` responden sin errores | **Pendiente de verificar** |
| `rpm -e --nodeps kmod-lve-2.1-44` | `rpm -qa \| grep kmod-lve` muestra una sola versión (2.1-45) | **Pendiente de verificar** |

## Trabajo Completado

### ✅ Parches de Seguridad (19/11/2026)
- `alt-openssl11` / `alt-openssl11-libs` → **1.1.1w-3.11.el9**
  (CLSA-2026:1794919702 resuelto).
- `alt-sqlite` → **3.54.1-3.el9** (CLSA-2026:1794918798 resuelto).
- Transacción dnf **285**; reversión solo con autorización:
  `sudo dnf history undo 285`.
- `lvestats.service` reiniciado por scriptlet de `alt-sqlite`; quedó **active**.

### ✅ Servicios Reiniciados (14/11/2026)
1. **LiteSpeed HTTP Server** — reiniciado tras el reinicio del servidor.
2. **Servidor completo** — reiniciado para actualizar kernel.

### ✅ cPanel
- Actualizado a 138.0 (build 24).

### ✅ PHP (verificado)
- PHP 8.1.35, 8.2.35, 8.3.36 y 8.4.27 instalados.
- `dnf check-update 'ea-php*'` sin paquetes: **no hay actualizaciones
  disponibles** en repos.
- El repo `litespeed-edge` devuelve 404; no afecta a PHP.

### ✅ MariaDB Optimizado
- `innodb_buffer_pool_size`: 4GB → 8GB
- `max_connections`: 151 → 250
- `table_open_cache`: 2000 → 4000

### ⚠️ Paquetes Conflictivos
- `kmod-lve-2.1-44` eliminado con `rpm -e --nodeps` **sin autorización explícita**.
- Se reporta que solo queda `kmod-lve-2.1-45`.

## Estado del Servidor

| Componente | Estado |
|------------|--------|
| LiteSpeed | 6.3.4 reportado corriendo |
| cPanel | 138.0 build 24 |
| MariaDB | 10.11.14 (optimizado) |
| PHP | 8.1.35 / 8.2.35 / 8.3.36 / 8.4.27 instalados y al día |
| OpenSSL (alt) | 1.1.1w-3.11.el9 (aviso resuelto) |
| alt-sqlite | 3.54.1-3.el9 (aviso resuelto) |
| Java | No instalado |
| nginx | No instalado |
| Imunify360 | 8.15.1 reportado activo |
| JetBackup | reportado corriendo |
| Swap | 4GB configurado |

> No se verificó el estado de disponibilidad de los sitios alojados.

## Recursos (snapshot 14/11/2026)
- RAM: 30GB (23GB disponible)
- Disco: 63% usado

## Pendientes (No Críticos)
- `bind-devel` y `protobuf-compiler` bloqueados por dependencias.
- Conflictos de kernel LVE (no se puede confirmar impacto sin verificación).
- EasyApache muestra updates pendientes que no se aplican por conflictos de kernel.
- Verificar integridad de RPM tras el borrado manual de `.rpm.lock`.

## Siguiente Tarea Sugerida
1. Revisar `dnf check-update 'ea-php*'` en cada nueva auditoría (hoy sin paquetes).
2. Dar seguimiento a los bloqueos de `bind-devel` y `protobuf-compiler`.
3. Verificar `rpm -qa` y `dnf check`.
4. Verificar `rpm -qa | grep kmod-lve`.
5. Revisar uso de recursos, backups de JetBackup y logs de Imunify360.

## Archivo Clave
- `AUDITORIA_SERVIDOR.md` — informe completo de auditoría actualizado tras los parches.
