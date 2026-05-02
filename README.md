# Nessus Docker — INY1105

Despliegue containerizado de Tenable Nessus con estrategia de persistencia dual.

## Arquitectura

| Componente | Estrategia | Ubicación en el host |
|---|---|---|
| Datos de Nessus (plugins, DB, config) | Named Volume `nessus_data` | `/var/lib/docker/volumes/nessus-docker_nessus_data/_data` |
| Logs del daemon | Bind Mount `./logs` | `./logs/` relativo al proyecto |

**Named Volume** → Los datos críticos sobreviven a `docker compose down`. Docker optimiza el I/O.  
**Bind Mount** → Los logs son accesibles directamente desde el host para integración con SIEM.

## Requisitos

- Docker >= 24.x
- Docker Compose >= 2.x
- 2 GB RAM disponible
- 10 GB espacio en disco (plugins de Nessus ocupan varios GB)

## Despliegue rápido

```bash
git clone https://github.com/TU-USUARIO/nessus-docker.git
cd nessus-docker
mkdir -p logs
docker compose up -d
```

Accede a la interfaz en: **https://localhost:8834**  
(Acepta la advertencia del certificado auto-firmado)

> **Primera vez:** Nessus solicita crear un usuario administrador y registrar el  
> Activation Code de Nessus Essentials (gratuito en tenable.com/products/nessus/nessus-essentials).

## Estructura del proyecto

```
nessus-docker/
├── Dockerfile          # Imagen base Debian + instalación Nessus
├── docker-compose.yml  # Orquestación y persistencia dual
├── .dockerignore       # Exclusiones del contexto de build
├── .gitignore          # Exclusiones del repositorio
├── README.md           # Este archivo
└── logs/               # Bind Mount — logs accesibles desde el host
```

## Comandos de operación

```bash
# Construir la imagen
docker compose build

# Iniciar en segundo plano
docker compose up -d

# Ver estado del servicio
docker compose ps

# Ver logs en tiempo real
docker compose logs -f nessus

# Verificar Bind Mount
ls -la ./logs/

# Verificar Named Volume
docker volume inspect nessus-docker_nessus_data

# Detener sin perder datos
docker compose stop

# Detener y eliminar contenedor (volúmenes persisten)
docker compose down

# ⚠️  Detener y eliminar TODO incluyendo plugins de Nessus
docker compose down -v
```

## Publicar en Docker Hub

```bash
docker tag nessus-docker-nessus:latest TU-USUARIO/nessus-iny1105:1.0.0
docker tag nessus-docker-nessus:latest TU-USUARIO/nessus-iny1105:latest
docker push TU-USUARIO/nessus-iny1105:1.0.0
docker push TU-USUARIO/nessus-iny1105:latest
```

---

*INY1105 — Infraestructura de Aplicaciones I*  
*DuocUC — Escuela de Informática y Telecomunicaciones*
