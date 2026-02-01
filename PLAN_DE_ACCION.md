# Plan de Accion - MisArriendos

## Estado Actual

- Repositorio inicializado con PRD completo (CLAUDE.MD)
- Cero codigo implementado
- Stack definido: React + Vite + Express + Prisma + PostgreSQL + Railway

---

## Fase 0: Inicializacion del Proyecto

### 0.1 Estructura monorepo
- Crear `package.json` raiz con workspaces
- Crear estructura `apps/frontend/` y `apps/backend/`
- Crear `packages/shared/types/` para tipos compartidos
- Configurar TypeScript en ambos proyectos

### 0.2 Backend - Setup base
- Inicializar Node.js + Express + TypeScript
- Instalar dependencias: express, cors, helmet, cookie-parser, zod, jsonwebtoken, bcryptjs
- Instalar dev deps: typescript, ts-node-dev, @types/*
- Crear `tsconfig.json`
- Crear estructura de carpetas: routes/, services/, middleware/, utils/, jobs/
- Crear `src/index.ts` con Express basico (healthcheck)
- Configurar scripts: `dev`, `build`, `start`

### 0.3 Frontend - Setup base
- Inicializar React 18 + Vite + TypeScript
- Instalar Tailwind CSS y configurar
- Instalar y configurar shadcn/ui
- Instalar React Router v6, React Query, React Hook Form, Zod, date-fns, Recharts
- Crear layout base (sidebar + topbar)
- Configurar rutas basicas
- Crear pagina placeholder para Login y Register

### 0.4 Base de datos
- Instalar Prisma en backend
- Crear `prisma/schema.prisma` con el schema completo del PRD
- Configurar `DATABASE_URL` con PostgreSQL local o Railway
- Ejecutar primera migracion: `npx prisma migrate dev --name init`
- Crear archivo `seed.ts` con datos de ejemplo

### 0.5 Configuracion de entorno
- Crear `.env.example` con todas las variables necesarias
- Crear `.gitignore` completo (node_modules, .env, dist, .prisma)
- Configurar CORS en backend para desarrollo local

**Entregable:** Proyecto corriendo en local, frontend en puerto 5173, backend en puerto 8080, DB conectada.

---

## Fase 1: Autenticacion (Sprint 1)

### 1.1 Backend Auth
- Crear `auth.service.ts`: hashear passwords con bcryptjs, generar JWT
- Crear middleware `requireAuth.ts`: verificar JWT desde httpOnly cookie
- Endpoints:
  - `POST /api/auth/register` - registro con email, password, nombre
  - `POST /api/auth/login` - login, setea cookie httpOnly
  - `POST /api/auth/logout` - elimina cookie
  - `GET /api/auth/me` - retorna usuario actual
  - `PUT /api/auth/me` - actualizar perfil
  - `POST /api/auth/forgot-password` - enviar email (placeholder)
  - `POST /api/auth/reset-password` - resetear password
- Validaciones Zod para cada endpoint

### 1.2 Frontend Auth
- Pagina Login con formulario (email + password)
- Pagina Register con formulario (nombre + email + password + confirmar)
- Hook `useAuth` para manejar estado de autenticacion
- Contexto `AuthProvider` con React Context
- Proteger rutas con componente `ProtectedRoute`
- Redirect a login si no autenticado
- Redirect a dashboard si ya autenticado

**Entregable:** Usuario puede registrarse, loguearse, y navegar al dashboard (vacio).

---

## Fase 2: Propiedades + Arrendatarios (Sprint 2)

### 2.1 Backend Propiedades
- CRUD completo `properties.ts`:
  - `GET /api/properties` - listar (con paginacion)
  - `POST /api/properties` - crear (todos los campos del PRD)
  - `GET /api/properties/:id` - detalle
  - `PUT /api/properties/:id` - actualizar
  - `DELETE /api/properties/:id` - eliminar
  - `PUT /api/properties/:id/status` - cambiar estado (RENTED/AVAILABLE/MAINTENANCE)
- Validaciones Zod para cada campo
- Verificar que la propiedad pertenece al usuario autenticado en cada operacion

### 2.2 Backend Arrendatarios
- Endpoints anidados en propiedad:
  - `POST /api/properties/:id/tenant` - crear/actualizar arrendatario
  - `GET /api/properties/:id/tenant` - ver arrendatario actual
  - `DELETE /api/properties/:id/tenant` - eliminar (propiedad pasa a AVAILABLE)

### 2.3 Frontend Propiedades
- Pagina lista de propiedades: cards con nombre, direccion, tipo, estado, monto
- Formulario completo crear/editar propiedad:
  - Seccion informacion basica (nombre, direccion, comuna, tipo)
  - Seccion arriendo (monto, dia pago, contrato, reajuste)
  - Seccion datos inversion (opcional, colapsable)
  - Seccion credito hipotecario (condicional al toggle "tiene credito")
  - Seccion arrendatario (nombre, telefono, email, RUT)
- Pagina detalle propiedad mostrando toda la info
- Boton cambiar estado de propiedad
- Formateador de moneda CLP (puntos como separador de miles)

**Entregable:** CRUD de propiedades funcionando con formulario completo. Se pueden agregar arrendatarios.

---

## Fase 3: Pagos y Dashboard (Sprint 3)

### 3.1 Backend Pagos
- CRUD Payments:
  - `GET /api/payments` - listar con filtros (propiedad, mes, anio, estado)
  - `POST /api/payments` - registrar pago
  - `GET /api/payments/:id` - detalle
  - `PUT /api/payments/:id` - actualizar
  - `DELETE /api/payments/:id` - eliminar
  - `GET /api/payments/pending` - pagos pendientes
  - `GET /api/payments/overdue` - pagos atrasados
- Logica de negocio:
  - Auto-generar registros de pago PENDING para cada propiedad arrendada al inicio del mes
  - Calcular dias de atraso
  - Transicion automatica PENDING -> OVERDUE cuando pasa la fecha
  - Validar pago parcial vs pago completo

### 3.2 Backend Dashboard
- Endpoint `GET /api/dashboard`:
  - Total por cobrar este mes
  - Total recibido este mes
  - Total pendiente
  - Lista de propiedades con estado de pago (semaforo)
  - Alertas activas

### 3.3 Frontend Dashboard
- Dashboard principal:
  - 3 cards: por cobrar, recibido, pendiente
  - Lista de propiedades con semaforo (verde/amarillo/rojo)
  - Seccion "Necesita tu atencion" con alertas
- Modal registrar pago:
  - Seleccionar propiedad y mes
  - Monto, fecha, medio de pago
  - Upload comprobante (placeholder por ahora)
- Vista pagos pendientes con boton "Registrar pago"
- Historial de pagos por propiedad (tabla)

**Entregable:** Dashboard funcional con semaforo de pagos. Se pueden registrar pagos y ver pendientes.

---

## Fase 4: Alertas y Emails (Sprint 4)

### 4.1 Backend Email
- Integrar Resend (`npm install resend`)
- Crear `email.service.ts` con funciones de envio
- Templates HTML para emails:
  - Pago atrasado (3, 7, 15 dias)
  - Contrato por vencer (60, 30, 15, 0 dias)
  - Reajuste pendiente
  - Contribuciones por vencer
  - Vacancia prolongada

### 4.2 Backend Jobs (cron)
- Integrar node-cron
- `checkOverduePayments.ts` - Diario 9:00 AM Chile:
  - Buscar pagos PENDING donde hoy > dueDate + 3 dias
  - Cambiar a OVERDUE
  - Crear alerta
  - Enviar email
- `checkExpiringContracts.ts` - Diario:
  - Buscar contratos que vencen en 60, 30, 15, 0 dias
  - Crear alerta + email
- Endpoint alertas:
  - `GET /api/alerts` - listar alertas del usuario
  - `PUT /api/alerts/:id/read` - marcar leida
  - `PUT /api/alerts/read-all` - marcar todas leidas

### 4.3 Frontend Alertas
- Icono campana en navbar con badge de no leidas
- Panel/dropdown de alertas
- Marcar como leida individual y masiva
- Alertas en dashboard en seccion "Necesita tu atencion"

**Entregable:** Sistema de alertas automatico. Emails se envian por atraso y vencimiento.

---

## Fase 5: Gastos y Balance de Caja (Sprint 5)

### 5.1 Backend Gastos
- CRUD Expenses:
  - `GET /api/expenses` - listar (filtros por propiedad, tipo, fecha)
  - `POST /api/expenses` - crear gasto
  - `PUT /api/expenses/:id` - actualizar
  - `DELETE /api/expenses/:id` - eliminar
  - `GET /api/properties/:id/expenses` - gastos de una propiedad
- Logica gastos recurrentes: auto-generar gastos mensuales/trimestrales/anuales
- Si la propiedad tiene credito hipotecario, auto-crear gasto MORTGAGE mensual

### 5.2 Backend Balance de Caja
- `GET /api/cashflow/monthly/:year/:month`:
  - Sumar ingresos (pagos recibidos)
  - Sumar egresos (gastos registrados)
  - Calcular flujo neto
  - Calcular reserva vacancia (% configurable)
  - Calcular disponible real
- `GET /api/cashflow/yearly/:year` - acumulado anual
- `GET /api/properties/:id/summary` - "cuanto me queda" por propiedad

### 5.3 Frontend Gastos
- Seccion gastos en detalle de propiedad
- Modal agregar gasto (tipo, descripcion, monto, fecha, recurrente)
- Widget "Cuanto me queda" en detalle propiedad

### 5.4 Frontend Balance de Caja
- Pagina Balance de Caja:
  - Navegacion por mes (flechas izq/der)
  - Tabla ingresos detallados
  - Tabla egresos detallados
  - Flujo neto, reserva vacancia, disponible real
  - Tooltip educativo sobre reserva vacancia
- Exportar a Excel (usando libreria xlsx o similar)

**Entregable:** Balance de caja mensual funcionando. Se pueden registrar gastos y ver "cuanto queda".

---

## Fase 6: Indicadores Economicos y Reajustes (Sprint 6)

### 6.1 Backend Indicadores
- Crear `indicators.service.ts`:
  - `getCurrentIndicators()` - obtener IPC, UF, UTM, dolar de mindicador.cl
  - `getIpcHistory(year)` - IPC historico
  - `calculateIpcAdjustment(monto, fechaDesde, fechaHasta)` - calcular reajuste
- Job `updateIndicators.ts` - diario, cachear indicadores en tabla Indicator
- Endpoints:
  - `GET /api/indicators/current` - indicadores del dia
  - `GET /api/indicators/ipc/:year` - IPC historico
  - `GET /api/indicators/calculate-adjustment` - calcular reajuste
  - `POST /api/indicators/adjustment-message` - generar mensaje para arrendatario

### 6.2 Backend Alertas Reajuste
- Job `checkPendingAdjustments.ts`:
  - Si ultimo reajuste + 11 meses < hoy -> crear alerta
- Job contribuciones:
  - 1 semana antes de abril, junio, septiembre, noviembre -> crear alerta

### 6.3 Frontend Reajustes
- Indicador visual "reajuste pendiente" en detalle propiedad
- Modal calculadora de reajuste:
  - Muestra IPC acumulado
  - Calcula nuevo monto
  - Genera mensaje de texto para copiar y enviar al arrendatario
  - Boton "Aplicar reajuste" que actualiza el monto de arriendo
- Indicadores actuales (UF, IPC) en sidebar o footer

**Entregable:** Reajuste IPC automatico. Alertas de contribuciones. Indicadores visibles.

---

## Fase 7: Valorizacion y Comparacion (Sprint 7)

### 7.1 Backend Valorizacion
- Endpoints:
  - `PUT /api/properties/:id/valuation` - actualizar valor actual
  - `GET /api/properties/:id/valuation-history` - historial de valores
  - `GET /api/properties/compare` - comparar todas las propiedades
- Logica:
  - Guardar cada actualizacion en PropertyValue
  - Calcular plusvalia (valor actual - precio compra)
  - Calcular rentabilidad % = (me queda anual / valor actual) x 100
  - Sugerir valor por IPC: precio_compra x (1 + IPC_acumulado)

### 7.2 Frontend Valorizacion
- Seccion valorizacion en detalle propiedad:
  - Precio compra, valor actual, plusvalia
  - Historial de valores (grafico con Recharts o tabla)
  - Boton "Actualizar valor" con sugerencia IPC
- Pagina Comparar Propiedades:
  - Tabla ordenable: propiedad, arriendo, me queda, rentabilidad %
  - Resumen portafolio (total valor, total arriendo, rentabilidad promedio)
  - Insight: "Tu estacionamiento tiene la mejor rentabilidad"

**Entregable:** Valorizacion con historial. Tabla comparativa de propiedades.

---

## Fase 8: Documentos y Reportes (Sprint 8)

### 8.1 Backend Documentos
- Configurar Cloudflare R2 para almacenamiento
- Endpoints:
  - `POST /api/documents` - subir documento (multipart/form-data)
  - `GET /api/documents` - listar documentos (filtrar por propiedad)
  - `DELETE /api/documents/:id` - eliminar
  - `GET /api/documents/:id/download` - descargar
- Limitar tamano de archivo (10MB)
- Tipos permitidos: PDF, JPG, PNG

### 8.2 Backend Reportes
- `GET /api/reports/annual/:year` - resumen anual consolidado
- `GET /api/reports/tax/:year` - reporte para contador (arriendos, contribuciones, intereses hipotecarios, mantenciones)
- `GET /api/reports/property/:id` - reporte individual
- Generacion de Excel con exceljs

### 8.3 Frontend Documentos
- Seccion documentos en detalle propiedad
- Upload con drag & drop
- Clasificar por tipo (contrato, cedula, inventario, etc.)
- Preview para imagenes, descarga para PDF

### 8.4 Frontend Reportes
- Pagina Reportes:
  - Resumen anual (tabla consolidada)
  - Boton descargar Excel para contador
  - Filtrar por anio

**Entregable:** Upload de documentos a R2. Reportes descargables en Excel.

---

## Fase 9: Vacancia y Portal Arrendatario (Sprint 9)

### 9.1 Backend Vacancia
- Cuando propiedad pasa a AVAILABLE:
  - Trackear gastos comunes como gasto del dueno
  - Calcular perdida mensual por vacancia
  - Job: alertar si lleva > 30 dias vacia

### 9.2 Portal Arrendatario (basico)
- Ruta publica con token unico por arrendatario
- El arrendatario puede:
  - Ver su estado de cuenta
  - Ver cuanto debe
  - Subir comprobante de pago
- NO puede: pagar online

**Entregable:** Gestion de vacancia. Portal basico para arrendatarios.

---

## Fase 10: Polish, Testing y Deploy (Sprint 10)

### 10.1 Frontend Polish
- Responsive completo (mobile-first)
- Loading states con skeletons
- Error boundaries
- Empty states con ilustraciones/mensajes utiles
- Formateadores: moneda CLP, fechas en espanol, RUT chileno
- Tooltips y guias iniciales (onboarding)

### 10.2 Backend Hardening
- Rate limiting (express-rate-limit)
- Helmet para headers de seguridad
- Validar todos los inputs con Zod
- Logging estructurado (pino o winston)
- Error handling centralizado
- Optimizar queries con Prisma (includes, selects)

### 10.3 Testing
- Tests unitarios para servicios criticos (pagos, reajustes, balance)
- Tests de integracion para endpoints principales
- Test manual completo de todos los flujos

### 10.4 Deploy a Railway
- Configurar servicio backend en Railway
- Configurar servicio frontend en Railway (build Vite)
- PostgreSQL en Railway
- Variables de entorno configuradas
- SSL automatico
- Backups automaticos de DB
- Error tracking con Sentry

### 10.5 Pre-launch
- Seed de datos de ejemplo para demo
- Verificar todos los flujos end-to-end
- Performance check

**Entregable:** Plataforma lista para produccion en Railway.

---

## Resumen de Fases

| Fase | Descripcion | Dependencias |
|------|-------------|--------------|
| 0 | Inicializacion del proyecto | Ninguna |
| 1 | Autenticacion | Fase 0 |
| 2 | Propiedades + Arrendatarios | Fase 1 |
| 3 | Pagos + Dashboard | Fase 2 |
| 4 | Alertas + Emails | Fase 3 |
| 5 | Gastos + Balance de Caja | Fase 3 |
| 6 | Indicadores + Reajustes | Fase 2 |
| 7 | Valorizacion + Comparacion | Fase 5, 6 |
| 8 | Documentos + Reportes | Fase 5 |
| 9 | Vacancia + Portal Arrendatario | Fase 3, 8 |
| 10 | Polish + Testing + Deploy | Todas |

### Fases paralelizables
- Fase 4 (alertas) y Fase 5 (gastos) pueden desarrollarse en paralelo despues de Fase 3
- Fase 6 (indicadores) puede avanzar en paralelo con Fase 4 y 5
- Fase 8 (documentos) puede avanzar en paralelo con Fase 7

---

## Orden Recomendado de Ejecucion

```
Fase 0 -> Fase 1 -> Fase 2 -> Fase 3
                                  |
                     +------------+------------+
                     |            |            |
                  Fase 4      Fase 5      Fase 6
                     |            |            |
                     +-------+----+----+-------+
                             |         |
                          Fase 7    Fase 8
                             |         |
                             +----+----+
                                  |
                               Fase 9
                                  |
                               Fase 10
```

---

## Proximos Pasos Inmediatos

Para empezar a construir la plataforma, el primer paso es ejecutar la **Fase 0** completa:

1. Crear la estructura de carpetas del monorepo
2. Inicializar package.json con workspaces
3. Setup backend Express + TypeScript
4. Setup frontend React + Vite + Tailwind + shadcn
5. Configurar Prisma con el schema completo
6. Ejecutar primera migracion
7. Verificar que todo corre en local
