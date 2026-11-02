# TechCare Solutions — Guía personal de conservación

**Proyecto académico:** Bases de Datos I (UMG) · **Año:** 2026  
**Repositorio personal:** https://github.com/Jers-0613/TechCare-Solutions  
**Repositorio original del equipo:** https://github.com/DU1T/TechCareDB1

> Esta guía complementa, pero **no sustituye**, el `README.md` principal. Es una referencia para entender, ejecutar y recuperar el proyecto más adelante.

## Qué es TechCare

Sistema para la administración de un taller de servicio técnico: clientes y equipos, órdenes de servicio, diagnósticos y reparaciones, repuestos por bodega, compras, kárdex, facturación, pagos, entrega, usuarios, permisos y reportes.

## Arquitectura

```text
Navegador (Nuxt 4)
      |
      v
API ASP.NET Core (.NET 8, JWT, Dapper)
      |
      v
SQL Server 2022 (tablas, SP, vistas, triggers y transacciones)
```

El frontend consume la API y no accede directamente a SQL Server. Las reglas críticas del negocio se implementaron en la base de datos mediante procedimientos almacenados y restricciones.

## Tecnologías

- **Frontend:** Nuxt 4 / Nuxt UI.
- **Backend:** C#, .NET 8, ASP.NET Core Web API, JWT, Dapper, Swagger/OpenAPI.
- **Base de datos:** Microsoft SQL Server 2022, procedimientos almacenados, triggers, vistas y transacciones.
- **Herramientas:** Git, PowerShell 7, Node.js/npm, Docker (opcional), Azure para despliegue.

## Estructura de archivos

| Ruta | Qué contiene |
| --- | --- |
| `frontend/` | Interfaz web y pantallas |
| `backend/TechCare.Api/` | API REST, autenticación y acceso a base de datos |
| `database/` | Estructura SQL, datos de demostración, scripts de reconstrucción y pruebas |
| `deploy/` | Scripts de publicación |
| `docs/` | Manual técnico, modelo ER, normalización y plan de pruebas |
| `README.md` | Manual principal con instalación, usuarios de prueba y comandos actualizados |

## Recuperar el proyecto en otra computadora

### 1. Clonar

```powershell
git clone https://github.com/Jers-0613/TechCare-Solutions.git
cd TechCare-Solutions
git switch master
```

### 2. Preparar SQL Server

Instala SQL Server 2022 (o inicia un contenedor SQL Server), PowerShell 7 y, si lo requiere tu entorno, `sqlcmd`. En un entorno **local y de prueba**, con autenticación integrada de Windows:

```powershell
cd database
./reconstruir.ps1 -Servidor localhost -Integrada -Base TechCare_Local
cd ..
```

Consulta `database/README.md` y el `README.md` principal para la variante de usuario `sa`. **Nunca reconstruyas la base de producción por accidente.**

### 3. Iniciar backend

Instala el SDK .NET 8 y ejecuta:

```powershell
cd backend/TechCare.Api
dotnet restore
dotnet user-secrets set "ConnectionStrings:TechCare" "Server=localhost;Database=TechCare_Local;Integrated Security=True;TrustServerCertificate=True"
dotnet user-secrets set "Jwt:Key" ([Convert]::ToBase64String([Security.Cryptography.RandomNumberGenerator]::GetBytes(48)))
dotnet run --launch-profile http
```

En desarrollo, Swagger está documentado en `http://localhost:5080/swagger`. Mantén la terminal abierta.

### 4. Iniciar frontend

Desde una **segunda terminal**, en la raíz del repositorio:

```powershell
cd frontend
npm ci
@"
NUXT_PUBLIC_API_BASE=http://localhost:5080/api
NUXT_PUBLIC_MODO_SIMULADO=false
"@ | Set-Content .env
npm run dev
```

Normalmente abre `http://localhost:3000`.

## Pruebas y documentación

- `docs/MANUAL_TECNICO.md`: documentación del sistema.
- `docs/PLAN_PRUEBAS.md`: escenarios y resultados esperados.
- `docs/ER_TechCare.pdf`: modelo entidad-relación.
- `docs/normalizacion.md`: decisiones de normalización.
- `database/pruebas/`: validaciones de la lógica SQL.
- `backend/pruebas/prueba_api_e2e.py`: pruebas integrales de API; ejecutar solo contra una base de prueba.

## Historia del respaldo

El repositorio personal se generó en noviembre de 2026 como copia independiente del repositorio colaborativo `DU1T/TechCareDB1`. Se copiaron las ramas del equipo (`a/`, `b/`, `c/`), `master` y las etiquetas `v1.0` y `v1.0.1`, con su historial Git. Los issues, pull requests y ajustes de GitHub no se transfieren con `git push --all` y `git push --tags`.

Para explorar cambios y versiones:

```powershell
git log --oneline -15
git branch -a
git tag -l
```

## Conservación y seguridad

- Nunca publiques credenciales de SQL Server, claves JWT, tokens o claves de Azure.
- Revisa también el **historial Git** antes de hacer público un proyecto; `.gitignore` no borra secretos ya publicados.
- Conserva aparte un respaldo cifrado de datos SQL cuando sea necesario; los scripts de creación no equivalen a un respaldo de los datos reales.
- Si vuelves a usarlo en producción, revisa dependencias, configuración, secretos, seguridad y disponibilidad del despliegue.
- El repositorio original es un trabajo de equipo: respeta los acuerdos de uso y atribución.

## Recordatorio

La primera lectura para retomar el proyecto debe ser el `README.md` principal, después `docs/MANUAL_TECNICO.md`, `database/README.md` y finalmente los archivos de código. Esta guía sirve como mapa general y registro personal de conservación, no como sustituto de la documentación técnica existente.
