# MisArriendos - PRD v3.0

## Documento Técnico para Claude Code

**Versión:** 3.0  
**Fecha:** Febrero 2025  
**Stack:** Railway (Backend + Frontend + PostgreSQL)

---

## 1. EL PROBLEMA REAL

### 1.1 Usuario Target

**Perfil:** Persona natural con 2-10 propiedades en arriendo en Chile.

- No es corredor profesional
- Tiene otro trabajo principal
- Administra arriendos en su tiempo libre
- Hoy usa: Excel + WhatsApp + carpeta con papeles + memoria

### 1.2 Sus Dolores Diarios (en orden de importancia)

| # | Dolor | Frecuencia | Consecuencia |
|---|-------|------------|--------------|
| 1 | "¿Me pagaron o no?" | Cada mes | 20+ min revisando cartola |
| 2 | "¿Cuánto me debe el moroso?" | Cuando hay atraso | Cobros incorrectos |
| 3 | "Se me olvidó el reajuste IPC" | Cada año | Pierde 4-5% de arriendo |
| 4 | "¿Cuándo vence el contrato?" | Ocasional | Contratos vencidos sin renovar |
| 5 | "¿Cuánto gano realmente?" | Nunca lo sabe | No sabe si le conviene |
| 6 | "¿Dónde está el contrato?" | Cuando lo necesita | Buscar 30+ minutos |
| 7 | "Se me pasó pagar contribuciones" | 4 veces al año | Paga con multa |

### 1.3 Propuesta de Valor

> **"Deja de perseguir pagos. Sabe exactamente cuánto ganas con tus arriendos."**

En una frase: **El Excel que te avisa y hace las cuentas por ti.**

### 1.4 Qué NO es MisArriendos

- ❌ No es para corredoras (ellas usan BReal, Leasity)
- ❌ No es para administrar edificios (ellos usan ComunidadFeliz)
- ❌ No es para publicar propiedades
- ❌ No requiere que el arrendatario haga nada
- ❌ No tiene integración WhatsApp (solo email)

---

## 2. FEATURES POR PRIORIDAD

### 2.1 MVP - Semanas 1-6

> **Objetivo:** Reemplazar el Excel. Responder "¿Me pagaron?"

#### Feature 1: Dashboard "¿Cómo van mis arriendos?"
```
Vista principal al entrar. Muestra:
- Total por cobrar este mes
- Total recibido
- Total pendiente
- Lista de propiedades con semáforo:
  🟢 Pagado
  🟡 Por vencer (faltan 1-5 días)
  🔴 Atrasado (pasó la fecha)
- Sección "Necesita tu atención" con alertas activas
```

#### Feature 2: Agregar propiedad
```
Campos básicos:
- Nombre/Alias ("Depto Providencia")
- Dirección
- Comuna
- Tipo (depto/casa/estacionamiento/bodega/otro)

Datos del arriendo:
- Arrendatario actual (nombre, teléfono, email)
- Monto arriendo mensual (CLP)
- Día de pago (ej: 5 de cada mes)
- Fecha inicio contrato
- Fecha fin contrato
- Tipo reajuste (IPC anual / UF / fijo % / sin reajuste)
- Mes de reajuste (1-12)
- Monto garantía

Datos de inversión (opcionales):
- Precio de compra
- Fecha de compra
- Valor actual estimado (manual, con sugerencia IPC)

Crédito hipotecario (opcional):
- ¿Tiene crédito? Sí/No
- Banco
- Monto original del crédito
- Tasa de interés anual (%)
- Tipo de tasa (fija/variable)
- Plazo total (años)
- Fecha inicio crédito
- Dividendo mensual actual (CLP)
- Saldo actual de deuda (CLP)
```

#### Feature 3: Registrar pago
```
- Seleccionar propiedad
- Seleccionar mes
- Monto pagado
- Fecha de pago
- Medio (transferencia/efectivo/otro)
- Foto comprobante (opcional)
- Si hay atraso, preguntar si cobrar multa
```

#### Feature 4: Ver quién debe y cuánto
```
Lista de pagos pendientes:
- Propiedad
- Mes(es) que debe
- Monto total adeudado
- Días de atraso
- Botón "Registrar pago"
```

#### Feature 5: Alertas por email
```
Enviar email automático cuando:
- Pago atrasado 3 días: "Pedro no ha pagado enero"
- Pago atrasado 7 días: recordatorio
- Pago atrasado 15 días: alerta urgente
```

#### Feature 6: Auth básico
```
- Registro con email/password
- Login
- Recuperar contraseña
```

---

### 2.2 Versión 1.1 - Semanas 7-10

> **Objetivo:** Automatizar lo tedioso. Reajustes y gastos.

#### Feature 7: Calculadora de reajuste IPC
```
- Sistema obtiene IPC actual desde mindicador.cl
- Calcula automático: arriendo_actual × (1 + IPC_acumulado)
- Genera mensaje para copiar y enviar al arrendatario
- Registra fecha de último reajuste
- Alerta cuando toca reajustar
```

#### Feature 8: Alerta contrato por vencer
```
Enviar email:
- 60 días antes: "Contrato vence en 2 meses"
- 30 días antes: "Contrato vence en 1 mes"
- 15 días antes: "Contrato vence en 2 semanas"
- Día del vencimiento: "Contrato venció hoy"
```

#### Feature 9: Registrar gastos fijos
```
Por cada propiedad, el DUEÑO paga:
- Dividendo hipoteca (viene del crédito si existe)
- Contribuciones (monto anual / 4)
- Seguro (opcional)
- Otros gastos fijos (definidos por usuario)

NOTA: Gastos comunes los paga el arrendatario, NO se trackean.

Campos:
- Tipo de gasto
- Descripción
- Monto
- Frecuencia (mensual/trimestral/anual)
- ¿Recurrente? Sí/No
```

#### Feature 10: Registrar gastos variables
```
Reparaciones, mantenciones, otros:
- Propiedad
- Descripción ("Cambio calefont")
- Monto
- Fecha
- Foto boleta (opcional)
```

#### Feature 11: "Cuánto me queda" por propiedad
```
Por cada propiedad:
  Arriendo mensual:        +$550.000
  Dividendo:               -$380.000
  Contribuciones/mes:      -$35.000
  Seguro/mes:              -$15.000
  ─────────────────────────────────
  TE QUEDA:                $120.000/mes

Vista anual con gastos variables incluidos.
```

#### Feature 12: Alerta reajuste pendiente
```
Si han pasado 11+ meses desde último reajuste:
- Mostrar en dashboard
- Enviar email: "Llevas 13 meses sin reajustar Depto Providencia"
```

---

### 2.3 Versión 1.2 - Semanas 11-14

> **Objetivo:** Balance de caja, documentos y reportes.

#### Feature 13: Balance de Caja Mensual
```
Vista consolidada de TODAS las propiedades:

┌─────────────────────────────────────────────────┐
│  BALANCE DE CAJA - Enero 2025                   │
├─────────────────────────────────────────────────┤
│  INGRESOS                                       │
│    Arriendo Depto Providencia    +$550.000      │
│    Arriendo Casa La Florida      +$450.000      │
│    ─────────────────────────────────────        │
│    TOTAL INGRESOS                +$1.000.000    │
│                                                 │
│  EGRESOS                                        │
│    Dividendo Depto Providencia   -$380.000      │
│    Dividendo Casa La Florida     -$320.000      │
│    Contribuciones (cuota ene)    -$120.000      │
│    ─────────────────────────────────────        │
│    TOTAL EGRESOS                 -$820.000      │
│                                                 │
│  ═══════════════════════════════════════════    │
│  FLUJO NETO DEL MES              +$180.000      │
│                                                 │
│  Reserva vacancia (4%)           -$40.000       │
│  ─────────────────────────────────────          │
│  DISPONIBLE REAL                 +$140.000      │
│                                                 │
│  ℹ️ La reserva de vacancia es una buena         │
│  práctica financiera. Guardas el 4% de tus     │
│  ingresos para cubrir meses sin arrendatario.  │
│  Así no te pilla desprevenido si alguien se va.│
└─────────────────────────────────────────────────┘

- Vista por mes (navegable)
- Vista acumulada del año
- Exportar a Excel
```

#### Feature 14: Vacancia (propiedad vacía)
```
Cuando una propiedad está sin arrendatario:
- Estado: DISPONIBLE
- Los gastos del dueño se siguen trackeando:
  - Dividendo (si hay crédito)
  - Contribuciones
  - Gastos comunes (ahora sí los paga el dueño)
  - Mantención
- Se muestra cuánto está "perdiendo" por mes vacío
- Alerta: "Llevas X días con Depto Providencia vacío"
```

#### Feature 15: Subir documentos
```
Por propiedad:
- Contrato de arriendo (PDF)
- Cédula arrendatario
- Inventario
- Otros

General:
- Escrituras
- Certificados
```

#### Feature 16: Historial de pagos
```
Por propiedad:
- Lista de todos los pagos recibidos
- Filtrar por año
- Ver comprobantes subidos
- Exportar a Excel
```

#### Feature 17: Resumen anual
```
Consolidado de todas las propiedades:
- Total recibido en el año
- Total gastado en el año
- Neto (lo que me quedó)
- Desglose por propiedad
- Tasa de pago a tiempo
- Meses con morosidad
```

#### Feature 18: Reporte para contador
```
Generar Excel/PDF con:
- Ingresos por arriendo (por propiedad)
- Contribuciones pagadas (deducible)
- Intereses hipotecarios (deducible) - calculado del crédito
- Gastos de mantención

Formato listo para declaración de renta.
```

---

### 2.4 Versión 2.0 - Semanas 15-20

> **Objetivo:** Visión de inversión.

#### Feature 19: Valorización de propiedad
```
Por cada propiedad con datos de compra:

┌─────────────────────────────────────────────────┐
│  VALORIZACIÓN - Depto Providencia               │
├─────────────────────────────────────────────────┤
│  Fecha de compra:       Marzo 2020              │
│  Precio de compra:      $48.000.000             │
│                                                 │
│  VALOR ACTUAL                                   │
│  Tu estimación:         $62.000.000             │
│  Sugerencia IPC:        $58.800.000             │
│  (precio compra + IPC acumulado 22.5%)          │
│                                                 │
│  PLUSVALÍA                                      │
│  En pesos:              +$14.000.000            │
│  En porcentaje:         +29.2%                  │
│  Años transcurridos:    4.8 años                │
│  Rentabilidad anual:    ~6.1%                   │
│                                                 │
│  📈 HISTORIAL                                   │
│  2020 (compra):  $48.000.000                    │
│  2021:           $50.400.000 (+5%)              │
│  2022:           $54.000.000 (+7%)              │
│  2023:           $57.500.000 (+6%)              │
│  2024:           $60.000.000 (+4%)              │
│  2025 (actual):  $62.000.000 (+3%)              │
│                                                 │
│  [Actualizar valor actual]                      │
└─────────────────────────────────────────────────┘

El historial se guarda cada vez que el usuario actualiza el valor.
```

#### Feature 20: Comparar propiedades
```
Tabla comparativa de todas las propiedades:

┌────────────────────┬───────────┬───────────┬───────────┐
│ Propiedad          │ Arriendo  │ Me queda  │ Rent. %   │
├────────────────────┼───────────┼───────────┼───────────┤
│ Depto Providencia  │ $550.000  │ $120.000  │ 2.4%      │
│ Casa La Florida    │ $450.000  │ $180.000  │ 3.8%      │
│ Estacionamiento    │ $80.000   │ $75.000   │ 9.0%      │
└────────────────────┴───────────┴───────────┴───────────┘

Rent. % = (Me queda anual / Valor actual) × 100

Ordenar por: Arriendo | Me queda | Rentabilidad %

💡 "Tu estacionamiento es tu mejor inversión en 
    términos de rentabilidad."
```

#### Feature 21: Recordatorio contribuciones
```
Alertar 1 semana antes del vencimiento de cada cuota:
- Cuota 1: Abril (vence ~30 abril)
- Cuota 2: Junio (vence ~30 junio)
- Cuota 3: Septiembre (vence ~30 sept)
- Cuota 4: Noviembre (vence ~30 nov)
```

#### Feature 22: Portal arrendatario (básico)
```
Arrendatario puede:
- Ver su estado de cuenta
- Ver cuánto debe
- Subir comprobante de pago

NO puede:
- Pagar online (complejidad adicional)
```

---

## 3. STACK TÉCNICO

### 3.1 Infraestructura

```
┌─────────────────────────────────────────────────────────────┐
│                        RAILWAY                               │
├─────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   Frontend   │  │   Backend    │  │  PostgreSQL  │       │
│  │   (React)    │  │   (Node.js)  │  │              │       │
│  │   Vite       │  │   Express    │  │              │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 Stack Detallado

```yaml
# Frontend
- React 18 + Vite
- Tailwind CSS + shadcn/ui
- React Router v6
- React Query (server state)
- React Hook Form + Zod
- date-fns (fechas)
- Recharts (gráficos)

# Backend
- Node.js 20 LTS
- Express.js
- Prisma ORM
- JWT auth (httpOnly cookies)
- Zod validation
- node-cron (jobs)

# Base de Datos
- PostgreSQL 15 (Railway)

# Servicios Externos
- Resend (emails) - gratis hasta 3k/mes
- Cloudflare R2 (archivos) - gratis hasta 10GB
- mindicador.cl (IPC, UF, UTM) - gratis, open source

# NO usar
- WhatsApp/Twilio (proceso de aprobación muy difícil)
```

### 3.3 Estructura del Proyecto

```
misarriendos/
├── apps/
│   ├── frontend/
│   │   ├── src/
│   │   │   ├── components/
│   │   │   │   ├── ui/              # shadcn components
│   │   │   │   ├── dashboard/       # Dashboard widgets
│   │   │   │   ├── properties/      # Property components
│   │   │   │   ├── payments/        # Payment components
│   │   │   │   ├── cashflow/        # Balance de caja
│   │   │   │   └── layout/          # Layout components
│   │   │   ├── pages/
│   │   │   │   ├── Dashboard.tsx
│   │   │   │   ├── Properties.tsx
│   │   │   │   ├── PropertyDetail.tsx
│   │   │   │   ├── Payments.tsx
│   │   │   │   ├── Expenses.tsx
│   │   │   │   ├── CashFlow.tsx
│   │   │   │   ├── Reports.tsx
│   │   │   │   ├── Compare.tsx
│   │   │   │   ├── Settings.tsx
│   │   │   │   ├── Login.tsx
│   │   │   │   └── Register.tsx
│   │   │   ├── hooks/
│   │   │   ├── services/            # API calls
│   │   │   ├── lib/                 # Utils
│   │   │   └── types/
│   │   ├── index.html
│   │   ├── vite.config.ts
│   │   └── package.json
│   │
│   └── backend/
│       ├── src/
│       │   ├── routes/
│       │   │   ├── auth.ts
│       │   │   ├── properties.ts
│       │   │   ├── tenants.ts
│       │   │   ├── payments.ts
│       │   │   ├── expenses.ts
│       │   │   ├── alerts.ts
│       │   │   ├── cashflow.ts
│       │   │   └── reports.ts
│       │   ├── services/
│       │   │   ├── auth.service.ts
│       │   │   ├── payment.service.ts
│       │   │   ├── indicators.service.ts  # mindicador.cl
│       │   │   ├── alert.service.ts
│       │   │   └── email.service.ts
│       │   ├── jobs/
│       │   │   ├── checkOverduePayments.ts
│       │   │   ├── checkExpiringContracts.ts
│       │   │   ├── checkPendingAdjustments.ts
│       │   │   └── updateIndicators.ts
│       │   ├── middleware/
│       │   ├── utils/
│       │   └── index.ts
│       ├── prisma/
│       │   ├── schema.prisma
│       │   └── seed.ts
│       └── package.json
│
├── packages/
│   └── shared/
│       └── types/
│
└── package.json
```

---

## 4. MODELO DE DATOS

### 4.1 Diagrama

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│    User     │────<│  Property   │────<│   Tenant    │
└─────────────┘     └──────┬──────┘     └─────────────┘
                          │
        ┌────────────┬────┴────┬────────────┐
        │            │         │            │
        ▼            ▼         ▼            ▼
┌─────────────┐ ┌─────────┐ ┌──────────┐ ┌───────────────┐
│   Payment   │ │ Expense │ │ Document │ │ PropertyValue │
└─────────────┘ └─────────┘ └──────────┘ └───────────────┘

┌─────────────┐   ┌─────────────┐
│    Alert    │   │  Indicator  │
└─────────────┘   └─────────────┘
```

### 4.2 Prisma Schema

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// =============================================
// USUARIOS
// =============================================

model User {
  id            String    @id @default(cuid())
  email         String    @unique
  passwordHash  String    @map("password_hash")
  name          String
  phone         String?
  
  // Plan
  plan          Plan      @default(FREE)
  trialEndsAt   DateTime? @map("trial_ends_at")
  
  // Preferencias
  emailAlerts       Boolean @default(true) @map("email_alerts")
  vacancyReserve    Float   @default(0.04) @map("vacancy_reserve") // 4% default
  
  // Timestamps
  createdAt     DateTime  @default(now()) @map("created_at")
  updatedAt     DateTime  @updatedAt @map("updated_at")
  
  // Relations
  properties    Property[]
  alerts        Alert[]
  
  @@map("users")
}

enum Plan {
  FREE        // 2 propiedades
  STARTER     // 5 propiedades
  PRO         // 15 propiedades
}

// =============================================
// PROPIEDADES
// =============================================

model Property {
  id              String    @id @default(cuid())
  userId          String    @map("user_id")
  
  // Info básica
  name            String              // "Depto Providencia"
  address         String
  comuna          String?
  type            PropertyType
  
  // Arriendo actual
  rentAmount      Int       @map("rent_amount")      // en pesos chilenos
  paymentDay      Int       @default(5) @map("payment_day")  // día del mes
  
  // Contrato
  contractStart   DateTime? @map("contract_start")
  contractEnd     DateTime? @map("contract_end")
  depositAmount   Int?      @map("deposit_amount")
  
  // Reajuste
  adjustmentType  AdjustmentType @default(IPC) @map("adjustment_type")
  adjustmentMonth Int?      @map("adjustment_month")  // mes del reajuste (1-12)
  lastAdjustment  DateTime? @map("last_adjustment")
  
  // === DATOS DE INVERSIÓN ===
  purchasePrice   Int?      @map("purchase_price")    // Precio de compra
  purchaseDate    DateTime? @map("purchase_date")     // Fecha de compra
  currentValue    Int?      @map("current_value")     // Valor actual estimado
  
  // === CRÉDITO HIPOTECARIO (opcional) ===
  hasMortgage         Boolean   @default(false) @map("has_mortgage")
  mortgageBank        String?   @map("mortgage_bank")
  mortgageOriginal    Int?      @map("mortgage_original")     // Monto original
  mortgageRate        Float?    @map("mortgage_rate")         // Tasa anual %
  mortgageRateType    RateType? @map("mortgage_rate_type")    // Fija/Variable
  mortgageYears       Int?      @map("mortgage_years")        // Plazo en años
  mortgageStartDate   DateTime? @map("mortgage_start_date")   // Fecha inicio
  mortgagePayment     Int?      @map("mortgage_payment")      // Dividendo mensual
  mortgageBalance     Int?      @map("mortgage_balance")      // Saldo actual
  
  // Estado
  status          PropertyStatus @default(RENTED)
  
  // Timestamps
  createdAt       DateTime  @default(now()) @map("created_at")
  updatedAt       DateTime  @updatedAt @map("updated_at")
  
  // Relations
  user            User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  tenant          Tenant?
  payments        Payment[]
  expenses        Expense[]
  documents       Document[]
  valueHistory    PropertyValue[]
  
  @@map("properties")
}

enum PropertyType {
  APARTMENT       // Departamento
  HOUSE           // Casa
  PARKING         // Estacionamiento
  STORAGE         // Bodega
  COMMERCIAL      // Local comercial
  OTHER           // Otro
}

enum AdjustmentType {
  IPC             // Reajuste por IPC
  UF              // Arriendo en UF
  FIXED           // Porcentaje fijo
  NONE            // Sin reajuste
}

enum RateType {
  FIXED           // Tasa fija
  VARIABLE        // Tasa variable
}

enum PropertyStatus {
  RENTED          // Arrendada
  AVAILABLE       // Disponible (vacante)
  MAINTENANCE     // En mantención
}

// =============================================
// HISTORIAL DE VALORIZACIÓN
// =============================================

model PropertyValue {
  id              String    @id @default(cuid())
  propertyId      String    @map("property_id")
  
  value           Int                 // Valor en CLP
  source          ValueSource         // Manual o IPC sugerido
  year            Int
  month           Int?                // Opcional, si es actualización específica
  
  createdAt       DateTime  @default(now()) @map("created_at")
  
  // Relations
  property        Property  @relation(fields: [propertyId], references: [id], onDelete: Cascade)
  
  @@map("property_values")
}

enum ValueSource {
  MANUAL          // Usuario ingresó el valor
  IPC_SUGGESTED   // Calculado por IPC
  INITIAL         // Valor de compra
}

// =============================================
// ARRENDATARIOS
// =============================================

model Tenant {
  id              String    @id @default(cuid())
  propertyId      String    @unique @map("property_id")
  
  // Info personal
  name            String
  rut             String?
  email           String?
  phone           String
  
  // Contacto emergencia
  emergencyName   String?   @map("emergency_name")
  emergencyPhone  String?   @map("emergency_phone")
  
  // Timestamps
  createdAt       DateTime  @default(now()) @map("created_at")
  updatedAt       DateTime  @updatedAt @map("updated_at")
  
  // Relations
  property        Property  @relation(fields: [propertyId], references: [id], onDelete: Cascade)
  
  @@map("tenants")
}

// =============================================
// PAGOS
// =============================================

model Payment {
  id              String    @id @default(cuid())
  propertyId      String    @map("property_id")
  
  // Período
  month           Int                 // 1-12
  year            Int                 // 2025
  
  // Montos
  amount          Int                 // Monto esperado
  amountPaid      Int?      @map("amount_paid")    // Monto pagado
  lateFee         Int?      @map("late_fee")       // Multa por atraso
  
  // Fechas
  dueDate         DateTime  @map("due_date")       // Fecha límite
  paidDate        DateTime? @map("paid_date")      // Fecha de pago
  
  // Detalles
  paymentMethod   PaymentMethod? @map("payment_method")
  reference       String?               // N° transferencia
  receiptUrl      String?   @map("receipt_url")    // Foto comprobante
  notes           String?
  
  // Estado
  status          PaymentStatus @default(PENDING)
  
  // Timestamps
  createdAt       DateTime  @default(now()) @map("created_at")
  updatedAt       DateTime  @updatedAt @map("updated_at")
  
  // Relations
  property        Property  @relation(fields: [propertyId], references: [id], onDelete: Cascade)
  
  @@unique([propertyId, month, year])
  @@map("payments")
}

enum PaymentMethod {
  TRANSFER        // Transferencia
  CASH            // Efectivo
  CHECK           // Cheque
  OTHER           // Otro
}

enum PaymentStatus {
  PENDING         // Pendiente
  PAID            // Pagado
  PARTIAL         // Pago parcial
  OVERDUE         // Atrasado
}

// =============================================
// GASTOS
// =============================================

model Expense {
  id              String    @id @default(cuid())
  propertyId      String    @map("property_id")
  
  // Detalle
  type            ExpenseType
  description     String?
  amount          Int
  date            DateTime
  
  // Recurrencia
  isRecurring     Boolean   @default(false) @map("is_recurring")
  frequency       ExpenseFrequency?
  
  // Documentación
  receiptUrl      String?   @map("receipt_url")
  
  // Para impuestos
  isTaxDeductible Boolean   @default(false) @map("is_tax_deductible")
  
  // Timestamps
  createdAt       DateTime  @default(now()) @map("created_at")
  
  // Relations
  property        Property  @relation(fields: [propertyId], references: [id], onDelete: Cascade)
  
  @@map("expenses")
}

enum ExpenseType {
  MORTGAGE        // Dividendo hipoteca (se puede auto-calcular)
  PROPERTY_TAX    // Contribuciones
  INSURANCE       // Seguro
  REPAIR          // Reparación
  MAINTENANCE     // Mantención
  COMMON_EXPENSES // Gastos comunes (solo en vacancia)
  OTHER           // Otro
}

enum ExpenseFrequency {
  MONTHLY         // Mensual
  QUARTERLY       // Trimestral
  YEARLY          // Anual
}

// =============================================
// DOCUMENTOS
// =============================================

model Document {
  id              String    @id @default(cuid())
  propertyId      String?   @map("property_id")
  userId          String    @map("user_id")
  
  // Info
  type            DocumentType
  name            String
  fileUrl         String    @map("file_url")
  fileSize        Int?      @map("file_size")
  
  // Timestamps
  createdAt       DateTime  @default(now()) @map("created_at")
  
  // Relations
  property        Property? @relation(fields: [propertyId], references: [id], onDelete: SetNull)
  
  @@map("documents")
}

enum DocumentType {
  CONTRACT        // Contrato arriendo
  ID_COPY         // Cédula
  INVENTORY       // Inventario
  RECEIPT         // Boleta/Factura
  DEED            // Escritura
  OTHER           // Otro
}

// =============================================
// ALERTAS
// =============================================

model Alert {
  id              String    @id @default(cuid())
  userId          String    @map("user_id")
  
  // Info
  type            AlertType
  title           String
  message         String
  propertyId      String?   @map("property_id")
  
  // Estado
  isRead          Boolean   @default(false) @map("is_read")
  emailSent       Boolean   @default(false) @map("email_sent")
  
  // Timestamps
  createdAt       DateTime  @default(now()) @map("created_at")
  readAt          DateTime? @map("read_at")
  
  // Relations
  user            User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  @@map("alerts")
}

enum AlertType {
  PAYMENT_OVERDUE     // Pago atrasado
  CONTRACT_EXPIRING   // Contrato por vencer
  ADJUSTMENT_DUE      // Reajuste pendiente
  PROPERTY_TAX_DUE    // Contribuciones por vencer
  VACANCY_ALERT       // Propiedad vacía mucho tiempo
  GENERAL             // General
}

// =============================================
// INDICADORES ECONÓMICOS (cache de mindicador.cl)
// =============================================

model Indicator {
  id              String    @id @default(cuid())
  code            String              // ipc, uf, utm, dolar
  date            DateTime
  value           Float
  
  createdAt       DateTime  @default(now()) @map("created_at")
  
  @@unique([code, date])
  @@map("indicators")
}
```

---

## 5. API ENDPOINTS

### 5.1 Autenticación

```
POST   /api/auth/register         # Registro
POST   /api/auth/login            # Login
POST   /api/auth/logout           # Logout
POST   /api/auth/forgot-password  # Recuperar contraseña
POST   /api/auth/reset-password   # Reset contraseña
GET    /api/auth/me               # Usuario actual
PUT    /api/auth/me               # Actualizar perfil
```

### 5.2 Propiedades

```
GET    /api/properties            # Listar propiedades
POST   /api/properties            # Crear propiedad
GET    /api/properties/:id        # Detalle propiedad
PUT    /api/properties/:id        # Actualizar propiedad
DELETE /api/properties/:id        # Eliminar propiedad
GET    /api/properties/:id/summary      # Resumen financiero
PUT    /api/properties/:id/status       # Cambiar estado (RENTED/AVAILABLE)
PUT    /api/properties/:id/valuation    # Actualizar valorización
GET    /api/properties/:id/valuation-history  # Historial valorización
GET    /api/properties/compare          # Comparar rentabilidad
```

### 5.3 Arrendatarios

```
POST   /api/properties/:id/tenant   # Crear/actualizar arrendatario
GET    /api/properties/:id/tenant   # Ver arrendatario
DELETE /api/properties/:id/tenant   # Eliminar arrendatario (vacancia)
```

### 5.4 Pagos

```
GET    /api/payments                # Listar pagos (con filtros)
POST   /api/payments                # Registrar pago
GET    /api/payments/:id            # Detalle pago
PUT    /api/payments/:id            # Actualizar pago
DELETE /api/payments/:id            # Eliminar pago
GET    /api/payments/pending        # Pagos pendientes
GET    /api/payments/overdue        # Pagos atrasados
```

### 5.5 Gastos

```
GET    /api/expenses                # Listar gastos
POST   /api/expenses                # Crear gasto
PUT    /api/expenses/:id            # Actualizar gasto
DELETE /api/expenses/:id            # Eliminar gasto
GET    /api/properties/:id/expenses # Gastos por propiedad
```

### 5.6 Balance de Caja

```
GET    /api/cashflow/monthly/:year/:month   # Balance mes específico
GET    /api/cashflow/yearly/:year           # Balance anual
GET    /api/cashflow/summary                # Resumen general
```

### 5.7 Documentos

```
GET    /api/documents               # Listar documentos
POST   /api/documents               # Subir documento
DELETE /api/documents/:id           # Eliminar documento
GET    /api/documents/:id/download  # Descargar documento
```

### 5.8 Alertas

```
GET    /api/alerts                  # Listar alertas
PUT    /api/alerts/:id/read         # Marcar como leída
PUT    /api/alerts/read-all         # Marcar todas como leídas
```

### 5.9 Dashboard y Reportes

```
GET    /api/dashboard               # Datos dashboard principal
GET    /api/reports/annual/:year    # Resumen anual
GET    /api/reports/tax/:year       # Reporte para impuestos
GET    /api/reports/property/:id    # Reporte por propiedad
```

### 5.10 Indicadores Económicos

```
GET    /api/indicators/current      # IPC, UF, UTM actuales
GET    /api/indicators/ipc/:year    # IPC histórico de un año
GET    /api/indicators/calculate-adjustment  # Calcular reajuste
POST   /api/indicators/adjustment-message    # Generar mensaje reajuste
```

---

## 6. API EXTERNA: mindicador.cl

### 6.1 Endpoints a usar

```javascript
// IPC actual
GET https://mindicador.cl/api/ipc

// UF actual
GET https://mindicador.cl/api/uf

// Todos los indicadores del día
GET https://mindicador.cl/api

// Histórico de un indicador por año
GET https://mindicador.cl/api/ipc/2024
```

### 6.2 Ejemplo de respuesta

```json
{
  "version": "1.7.0",
  "autor": "mindicador.cl",
  "codigo": "ipc",
  "nombre": "Índice de Precios al Consumidor",
  "unidad_medida": "Porcentaje",
  "serie": [
    { "fecha": "2024-12-01T03:00:00.000Z", "valor": 0.2 },
    { "fecha": "2024-11-01T03:00:00.000Z", "valor": 0.3 }
  ]
}
```

### 6.3 Servicio de indicadores

```typescript
// services/indicators.service.ts

const MINDICADOR_URL = 'https://mindicador.cl/api';

export async function getCurrentIndicators() {
  const response = await fetch(MINDICADOR_URL);
  const data = await response.json();
  
  return {
    uf: data.uf.valor,
    ipc: data.ipc.valor,
    utm: data.utm.valor,
    dolar: data.dolar.valor,
    fecha: new Date()
  };
}

export async function getIpcHistory(year: number) {
  const response = await fetch(`${MINDICADOR_URL}/ipc/${year}`);
  const data = await response.json();
  return data.serie;
}

export async function calculateIpcAdjustment(
  originalAmount: number,
  fromDate: Date,
  toDate: Date
): Promise<{ newAmount: number; ipcAccumulated: number }> {
  // Obtener IPC acumulado entre fechas
  // Calcular nuevo monto
  // Retornar resultado
}
```

---

## 7. JOBS AUTOMÁTICOS

```javascript
// Ejecutar diariamente a las 9:00 AM Chile

// Job 1: Verificar pagos atrasados
// Si hoy > fecha_pago + 3 días y status = PENDING
// → Cambiar status a OVERDUE
// → Crear alerta
// → Enviar email

// Job 2: Verificar contratos por vencer
// Si fecha_fin - hoy = 60, 30, 15, 0 días
// → Crear alerta
// → Enviar email

// Job 3: Verificar reajustes pendientes
// Si última_fecha_reajuste + 11 meses < hoy
// → Crear alerta
// → Enviar email

// Job 4: Verificar contribuciones
// 1 semana antes de abril, junio, septiembre, noviembre
// → Crear alerta
// → Enviar email

// Job 5: Actualizar indicadores desde mindicador.cl
// Una vez al día
// → Obtener IPC, UF, UTM, dólar
// → Guardar en tabla Indicator

// Job 6: Alertar vacancia prolongada
// Si propiedad status = AVAILABLE por > 30 días
// → Crear alerta
```

---

## 8. PANTALLAS UI

### 8.1 Dashboard (página principal)

```
┌─────────────────────────────────────────────────────────────┐
│  MisArriendos                           [Javier ▼] [⚙️]     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Enero 2025                                                 │
│  ───────────                                                │
│  Por cobrar: $1.650.000                                     │
│  Recibido:   $1.100.000                                     │
│  Pendiente:  $550.000                                       │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ 🟢 Depto Providencia    $550.000   Pagado 3/ene     │  │
│  │ 🟢 Depto Ñuñoa          $650.000   Pagado 5/ene     │  │
│  │ 🔴 Casa La Florida      $450.000   8 días atraso    │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  ⚠️ Necesita tu atención                                   │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ • Casa La Florida: Mario no ha pagado (8 días)      │  │
│  │ • Depto Providencia: Reajuste pendiente desde dic   │  │
│  │ • Depto Ñuñoa: Contrato vence en 45 días            │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  [+ Nueva propiedad]                                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 8.2 Balance de Caja

```
┌─────────────────────────────────────────────────────────────┐
│  Balance de Caja                      [◀ Dic 2024] Ene 2025 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  INGRESOS                                                   │
│  ─────────                                                  │
│  Arriendo Depto Providencia           +$550.000             │
│  Arriendo Depto Ñuñoa                 +$650.000             │
│  Arriendo Casa La Florida             +$450.000             │
│  ────────────────────────────────────────────────           │
│  TOTAL INGRESOS                       +$1.650.000           │
│                                                             │
│  EGRESOS                                                    │
│  ───────                                                    │
│  Dividendo Depto Providencia          -$380.000             │
│  Dividendo Depto Ñuñoa                -$420.000             │
│  Dividendo Casa La Florida            -$320.000             │
│  Contribuciones (cuota enero)         -$180.000             │
│  Reparación calefont (La Florida)     -$85.000              │
│  ────────────────────────────────────────────────           │
│  TOTAL EGRESOS                        -$1.385.000           │
│                                                             │
│  ═══════════════════════════════════════════════════════    │
│  FLUJO NETO DEL MES                   +$265.000             │
│                                                             │
│  Reserva vacancia (4%)                -$66.000              │
│  ────────────────────────────────────────────────           │
│  DISPONIBLE REAL                      +$199.000             │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ ℹ️ Reserva de Vacancia                               │  │
│  │                                                      │  │
│  │ Guardar el 4% de tus ingresos es una buena práctica │  │
│  │ financiera. Este fondo te permite cubrir los meses  │  │
│  │ en que una propiedad quede vacía, sin afectar tu    │  │
│  │ flujo de caja personal.                             │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                             │
│  [Exportar Excel]                                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 8.3 Detalle Propiedad (con crédito)

```
┌─────────────────────────────────────────────────────────────┐
│  ← Volver                    Depto Providencia    [Editar]  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  📍 Av. Providencia 1234, depto 45                         │
│  👤 Juan Pérez · 912345678 · juan@email.com                │
│  Estado: 🟢 Arrendado                                       │
│                                                             │
│  ────────────────────────────────────────────               │
│  CONTRATO                                                   │
│  ────────────────────────────────────────────               │
│  Arriendo:     $550.000/mes                                 │
│  Día pago:     5 de cada mes                                │
│  Inicio:       1 marzo 2023                                 │
│  Término:      28 febrero 2025 (en 58 días)                 │
│  Reajuste:     IPC anual (mes: enero)                       │
│                                                             │
│  ⚠️ Próximo reajuste: ¡Ahora! (+4.2% = $573.100)           │
│     [Calcular y aplicar reajuste]                           │
│                                                             │
│  ────────────────────────────────────────────               │
│  CRÉDITO HIPOTECARIO                                        │
│  ────────────────────────────────────────────               │
│  Banco:            Banco Estado                             │
│  Monto original:   $38.400.000                              │
│  Tasa:             4.5% anual (fija)                        │
│  Plazo:            20 años                                  │
│  Inicio:           Marzo 2020                               │
│  Dividendo:        $380.000/mes                             │
│  Saldo actual:     $31.200.000                              │
│                                                             │
│  ────────────────────────────────────────────               │
│  ¿CUÁNTO ME QUEDA?                                         │
│  ────────────────────────────────────────────               │
│  Arriendo:              +$550.000                           │
│  Dividendo:             -$380.000                           │
│  Contribuciones/mes:    -$35.000                            │
│  ─────────────────────────────────                          │
│  ME QUEDA:              $135.000/mes                        │
│                         $1.620.000/año                      │
│                                                             │
│  ────────────────────────────────────────────               │
│  VALORIZACIÓN                                               │
│  ────────────────────────────────────────────               │
│  Precio compra (2020):  $48.000.000                         │
│  Valor actual:          $62.000.000                         │
│  Plusvalía:             +$14.000.000 (+29.2%)               │
│  [Actualizar valor] [Ver historial]                         │
│                                                             │
│  [Pagos] [Gastos] [Documentos]                              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 8.4 Comparar Propiedades

```
┌─────────────────────────────────────────────────────────────┐
│  Comparar Propiedades                   Ordenar: [Rent. %▼] │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌────────────────┬───────────┬───────────┬───────────────┐│
│  │ Propiedad      │ Arriendo  │ Me queda  │ Rentabilidad  ││
│  ├────────────────┼───────────┼───────────┼───────────────┤│
│  │ Estacionam.    │ $80.000   │ $75.000   │ 9.0% 🏆       ││
│  │ Casa La Florida│ $450.000  │ $180.000  │ 3.8%          ││
│  │ Depto Ñuñoa    │ $650.000  │ $150.000  │ 2.8%          ││
│  │ Depto Provid.  │ $550.000  │ $135.000  │ 2.6%          ││
│  └────────────────┴───────────┴───────────┴───────────────┘│
│                                                             │
│  💡 Tu estacionamiento tiene la mejor rentabilidad (9%).   │
│     Los departamentos rondan el 2.6%-2.8%.                  │
│                                                             │
│  ────────────────────────────────────────────               │
│  RESUMEN PORTAFOLIO                                         │
│  ────────────────────────────────────────────               │
│  Total propiedades:     4                                   │
│  Valor total:           $172.000.000                        │
│  Arriendo total:        $1.730.000/mes                      │
│  Me queda total:        $540.000/mes                        │
│  Rentabilidad promedio: 3.8%                                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 8.5 Formulario Nueva Propiedad (con crédito)

```
┌─────────────────────────────────────────────────────────────┐
│  Nueva Propiedad                                       [X]  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  INFORMACIÓN BÁSICA                                         │
│  ──────────────────                                         │
│  Nombre            [Depto Providencia          ]           │
│  Dirección         [Av. Providencia 1234, d.45 ]           │
│  Comuna            [Providencia               ▼]           │
│  Tipo              [Departamento              ▼]           │
│                                                             │
│  ARRIENDO                                                   │
│  ────────                                                   │
│  Arrendatario      [Juan Pérez                 ]           │
│  Teléfono          [+56 9 1234 5678            ]           │
│  Email             [juan@email.com             ]           │
│  Monto arriendo    [$550.000                   ]           │
│  Día de pago       [5                         ▼]           │
│  Inicio contrato   [01/03/2023                 ]           │
│  Fin contrato      [28/02/2025                 ]           │
│  Tipo reajuste     [IPC anual                 ▼]           │
│  Mes reajuste      [Enero                     ▼]           │
│  Garantía          [$1.100.000                 ]           │
│                                                             │
│  DATOS DE INVERSIÓN (opcional)                              │
│  ─────────────────────────────                              │
│  Precio de compra  [$48.000.000                ]           │
│  Fecha de compra   [15/03/2020                 ]           │
│  Valor actual      [$62.000.000                ]           │
│                    💡 Sugerencia IPC: $58.800.000           │
│                                                             │
│  CRÉDITO HIPOTECARIO                                        │
│  ───────────────────                                        │
│  ¿Tiene crédito?   [✓] Sí                                  │
│                                                             │
│  Banco             [Banco Estado              ▼]           │
│  Monto original    [$38.400.000                ]           │
│  Tasa interés      [4.5         ] % anual                  │
│  Tipo de tasa      [Fija                      ▼]           │
│  Plazo             [20          ] años                     │
│  Fecha inicio      [15/03/2020                 ]           │
│  Dividendo mensual [$380.000                   ]           │
│  Saldo actual      [$31.200.000                ]           │
│                                                             │
│                              [Cancelar]  [Guardar]         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 9. CONFIGURACIÓN

### 9.1 Variables de Entorno

```bash
# Backend
NODE_ENV=production
PORT=8080
DATABASE_URL=postgresql://...
JWT_SECRET=super-secret-key
JWT_EXPIRES_IN=7d

# Email
RESEND_API_KEY=re_xxxxx
EMAIL_FROM="MisArriendos <noreply@misarriendos.cl>"

# Storage
R2_ACCOUNT_ID=xxxxx
R2_ACCESS_KEY=xxxxx
R2_SECRET_KEY=xxxxx
R2_BUCKET=misarriendos

# Frontend URL
FRONTEND_URL=https://app.misarriendos.cl

# Indicadores (no requiere key)
MINDICADOR_URL=https://mindicador.cl/api

# Frontend
VITE_API_URL=https://api.misarriendos.cl
```

### 9.2 Railway Setup

```json
// railway.json
{
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "restartPolicyType": "ON_FAILURE"
  }
}
```

---

## 10. PRICING

| Plan | Precio | Propiedades | Features |
|------|--------|-------------|----------|
| **Gratis** | $0 | 2 | Dashboard, pagos, alertas email |
| **Básico** | $9.990/mes | 5 | + Reajuste IPC + Gastos + Documentos |
| **Pro** | $19.990/mes | 15 | + Balance caja + Comparar + Reportes |

---

## 11. TO-DO LIST POR SPRINT

### Sprint 1: Setup + Auth (Semana 1)

```markdown
## Infraestructura
- [ ] Crear proyecto Railway
- [ ] Crear PostgreSQL en Railway
- [ ] Configurar dominio (si hay)
- [ ] Setup repo GitHub

## Backend Base
- [ ] Inicializar Node.js + Express + TypeScript
- [ ] Configurar Prisma
- [ ] Crear schema inicial (completo v3)
- [ ] Ejecutar primera migración
- [ ] Setup JWT auth
- [ ] Endpoints: register, login, logout, me

## Frontend Base
- [ ] Inicializar React + Vite + TypeScript
- [ ] Instalar Tailwind + shadcn/ui
- [ ] Crear layout base (sidebar, navbar)
- [ ] Páginas: Login, Register
- [ ] Configurar React Router
- [ ] Configurar React Query
```

### Sprint 2: Propiedades + Arrendatarios (Semana 2)

```markdown
## Backend
- [ ] CRUD Properties (con todos los campos v3)
- [ ] CRUD Tenants (nested en property)
- [ ] Validaciones con Zod
- [ ] Endpoint cambiar estado (RENTED/AVAILABLE)

## Frontend
- [ ] Página lista propiedades (cards)
- [ ] Modal/página crear propiedad (formulario completo)
- [ ] Sección crédito hipotecario condicional
- [ ] Página detalle propiedad
- [ ] Formulario arrendatario
- [ ] Estados: arrendado/disponible
```

### Sprint 3: Pagos (Semana 3-4)

```markdown
## Backend
- [ ] CRUD Payments
- [ ] Lógica: generar pagos mensuales
- [ ] Lógica: calcular días atraso
- [ ] Lógica: actualizar status (PENDING → OVERDUE)
- [ ] Endpoint: pagos pendientes
- [ ] Endpoint: pagos atrasados

## Frontend
- [ ] Dashboard principal (semáforo)
- [ ] Semáforo de pagos (verde/amarillo/rojo)
- [ ] Modal registrar pago
- [ ] Historial de pagos por propiedad
- [ ] Widget "pendientes" en dashboard
```

### Sprint 4: Alertas + Email (Semana 5)

```markdown
## Backend
- [ ] Modelo Alert
- [ ] Integrar Resend
- [ ] Templates email:
  - Pago atrasado
  - Contrato por vencer
  - Reajuste pendiente
  - Contribuciones por vencer
  - Vacancia prolongada
- [ ] Job: verificar pagos atrasados (cron diario)
- [ ] Job: verificar contratos por vencer
- [ ] Job: verificar vacancia prolongada

## Frontend
- [ ] Centro de notificaciones
- [ ] Badge de alertas no leídas
- [ ] Marcar como leída
```

### Sprint 5: Gastos + Balance Caja (Semana 6-7)

```markdown
## Backend
- [ ] CRUD Expenses
- [ ] Lógica: gastos recurrentes
- [ ] Lógica: auto-crear gasto dividendo si hay crédito
- [ ] Endpoint: resumen financiero por propiedad
- [ ] Endpoint: balance de caja mensual
- [ ] Endpoint: balance de caja anual
- [ ] Cálculo reserva vacancia (4% configurable)

## Frontend
- [ ] Página/sección gastos por propiedad
- [ ] Modal agregar gasto
- [ ] Widget "cuánto me queda" en detalle propiedad
- [ ] Página Balance de Caja
- [ ] Navegación por mes
- [ ] Explicación reserva vacancia
- [ ] Export Excel
```

### Sprint 6: Indicadores + Reajustes (Semana 8-9)

```markdown
## Backend
- [ ] Servicio mindicador.cl
- [ ] Job: actualizar indicadores diario
- [ ] Modelo Indicator (cache)
- [ ] Endpoint: calcular reajuste IPC
- [ ] Endpoint: aplicar reajuste
- [ ] Generar mensaje para arrendatario
- [ ] Alerta de reajuste pendiente
- [ ] Alerta contribuciones

## Frontend
- [ ] Indicador "reajuste pendiente" en propiedad
- [ ] Modal calculadora de reajuste
- [ ] Botón copiar mensaje
- [ ] Indicadores actuales (UF, IPC) en footer/sidebar
```

### Sprint 7: Valorización + Comparar (Semana 10-11)

```markdown
## Backend
- [ ] Modelo PropertyValue
- [ ] Endpoint: actualizar valorización
- [ ] Endpoint: historial valorización
- [ ] Endpoint: comparar propiedades
- [ ] Cálculo rentabilidad %
- [ ] Sugerencia valor por IPC acumulado

## Frontend
- [ ] Sección valorización en propiedad
- [ ] Modal actualizar valor
- [ ] Historial de valores (gráfico o lista)
- [ ] Página Comparar Propiedades
- [ ] Tabla ordenable
- [ ] Resumen portafolio
```

### Sprint 8: Documentos + Reportes (Semana 12-13)

```markdown
## Backend
- [ ] Configurar Cloudflare R2
- [ ] Upload de archivos
- [ ] CRUD Documents
- [ ] Asociar a propiedad
- [ ] Endpoint: resumen anual
- [ ] Endpoint: reporte para contador
- [ ] Export Excel (usando exceljs)

## Frontend
- [ ] Sección documentos en propiedad
- [ ] Upload con drag & drop
- [ ] Preview/download
- [ ] Tipos: contrato, cédula, etc
- [ ] Página reportes
- [ ] Resumen anual
- [ ] Descargar Excel/PDF
```

### Sprint 9: Polish + Testing (Semana 14)

```markdown
## General
- [ ] Responsive completo
- [ ] Loading states
- [ ] Error handling
- [ ] Validaciones frontend
- [ ] Testing manual completo
- [ ] Fix bugs
- [ ] Optimizar queries
- [ ] Revisar UX/UI

## Pre-launch
- [ ] SSL configurado
- [ ] Backups automáticos DB
- [ ] Error tracking (Sentry)
- [ ] Rate limiting
- [ ] Logs
```

---

## 12. CHECKLIST PRE-LAUNCH

```markdown
## Técnico
- [ ] SSL configurado
- [ ] Backups automáticos DB
- [ ] Error tracking (Sentry)
- [ ] Rate limiting
- [ ] Logs

## Legal
- [ ] Términos de servicio
- [ ] Política de privacidad

## Marketing
- [ ] Landing page
- [ ] Copy final
- [ ] Screenshots
- [ ] Video demo (opcional)

## Onboarding
- [ ] Email de bienvenida
- [ ] Datos de ejemplo (seed)
- [ ] Tooltips/guía inicial
```

---

## 13. COMANDOS ÚTILES

```bash
# Desarrollo local
cd apps/backend && npm run dev
cd apps/frontend && npm run dev

# Prisma
npx prisma migrate dev --name init
npx prisma generate
npx prisma studio

# Deploy
git push origin main  # Railway auto-deploy

# Seed data
npx prisma db seed
```

---

## 14. NOTAS IMPORTANTES

### Gastos Comunes
- **Cuando hay arrendatario:** Los paga el arrendatario, NO se trackean
- **Cuando está vacío:** Los paga el dueño, SÍ se trackean como gasto

### Contribuciones
- Siempre las paga el dueño
- Son deducibles de impuestos
- Recordar fechas: abril, junio, septiembre, noviembre

### Reserva de Vacancia
- Default: 4% de los ingresos
- Configurable por usuario
- Propósito: cubrir meses sin arrendatario
- Se muestra con explicación educativa

### Valorización
- Input manual por usuario
- Sistema sugiere: precio_compra × (1 + IPC_acumulado)
- Se guarda historial cada vez que se actualiza

### Sin WhatsApp
- Meta tiene proceso de aprobación muy difícil
- Solo alertas por email (Resend)
- Posible agregar en futuro si hay demanda

---

**Documento listo para Claude Code.**

*Versión 3.0 - Febrero 2025*
