# Plan: AgendaOS — SaaS de Agenda de Citas

## Cuándo usar este plan
Cuando se pide agregar features, módulos o fixes a AgendaOS (app.agendaos.pro).

## Contexto del proyecto
- URL: https://app.agendaos.pro
- Repo: alexanderestradaconsuegra-tech/autix-agenda → index.html
- DB: postgres://agenda_user:Agenda2025@market-saas_merka-db:5432/agenda
- Deploy: EasyPanel mercado-saas/agenda-app
- Negocios activos: demo/demo2025, autix/autix2025

## Paleta de colores
```css
:root {
  --bg: #f4faf5;
  --surface: #ffffff;
  --border: #d1fae5;
  --ink: #023337;
  --accent: #4ea674;
  --accent-hover: #3d8a60;
  --light: #c0e6b9;
  --pale: #e9f8e7;
  --text: #023337;
  --text-muted: #6b7280;
  --success: #22c55e;
  --error: #ef4444;
  --radius: 12px;
  --font: 'Geist', sans-serif;
  --font-mono: 'Geist Mono', monospace;
}
```

## Endpoints n8n activos
- /webhook/agenda-auth
- /webhook/agenda-servicios
- /webhook/agenda-profesionales
- /webhook/agenda-clientes
- /webhook/agenda-reservas
- /webhook/agenda-slots
- /webhook/agenda-reserva-publica
- /webhook/agenda-dashboard

## Schema de tablas principales
```sql
negocios (id, org_id, password, nombre, slug, rubro, plan, activo, trial_start)
profesionales (id, org_id, nombre, email, telefono, activo)
servicios (id, org_id, nombre, duracion_min, precio, requiere_pago, activo)
disponibilidad (id, org_id, profesional_id, dia_semana, hora_inicio, hora_fin)
reservas (id, org_id, cliente_nombre, servicio_id, profesional_id, fecha_inicio, fecha_fin, estado, monto)
pagos (id, org_id, reserva_id, monto, mp_payment_id, estado)
```

## Función clave slots
```sql
SELECT hora_inicio::TEXT, hora_fin::TEXT
FROM fn_slots_disponibles('org_id', profesional_id, 'YYYY-MM-DD'::DATE, duracion_min)
WHERE disponible = true;
```

## Módulos del sistema
1. Dashboard — métricas, reservas del día
2. Reservas — CRUD, estados, calendario
3. Profesionales — CRUD, disponibilidad horaria
4. Servicios — CRUD, precios, duración
5. Clientes — CRUD, historial
6. Página pública de reserva — slug único por negocio
7. Agente WhatsApp — Evolution API por instancia

## Planes de suscripción
- Pro: links wa.me, funciones base
- Elite: instancia Evolution API dedicada por negocio

## Onboarding nuevo cliente
```sql
INSERT INTO negocios (org_id, password, nombre, slug, rubro, plan)
VALUES ('cliente', 'pass', 'Nombre', 'slug-url', 'barberia', 'pro');
-- Luego agregar profesionales, servicios y disponibilidad
```

## Deploy
```bash
curl -L "https://raw.githubusercontent.com/alexanderestradaconsuegra-tech/autix-agenda/main/index.html" \
  -o /usr/share/nginx/html/index.html && echo "LISTO"
```
