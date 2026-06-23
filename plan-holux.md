# Plan: SaaS Web Genérico (Holux)

## Cuándo usar este plan
Cuando se pide crear un nuevo SaaS web desde cero sin template específico.

## Stack obligatorio
- Frontend: HTML/CSS/JS vanilla, single-file index.html
- Backend: n8n webhooks en https://n8n-n8n.fa2cjf.easypanel.host/webhook/
- DB: PostgreSQL multi-tenant con org_id
- Deploy: EasyPanel + Nginx, GitHub repo alexanderestradaconsuegra-tech

## Pasos de ejecución (en orden, sin saltear)

### 1. Definir estructura
- Nombre del proyecto y org_id base
- Módulos principales (máximo 5 para MVP)
- Tablas PostgreSQL necesarias

### 2. Crear schema SQL completo
```sql
-- Tabla base obligatoria
CREATE TABLE negocios (
  id SERIAL PRIMARY KEY,
  org_id VARCHAR(50) UNIQUE NOT NULL,
  password VARCHAR(100) NOT NULL,
  nombre VARCHAR(100),
  plan VARCHAR(20) DEFAULT 'pro',
  activo BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Todas las tablas del dominio con org_id
-- ON CONFLICT para evitar duplicados
-- GRANT ALL ON ALL TABLES IN SCHEMA public TO usuario;
```

### 3. Crear workflows n8n
Patrón estándar para CADA endpoint:
```
Webhook (responseMode: responseNode)
→ Code JS: const b = $input.first().json.body; return [{ json: { ...campos } }];
→ Switch: Left={{ $json.action }} Right=texto_plano
→ Code → Postgres → Respond to Webhook
```
Reglas críticas:
- webhookId único por workflow
- Switch rightValue siempre texto plano sin = al inicio
- Respond: ={{ JSON.stringify({ ok: true, data: $input.all().map(i => i.json) }) }}

### 4. Crear index.html (single-file completo)

#### Estructura HTML obligatoria
```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nombre App</title>
  <link href="https://fonts.googleapis.com/css2?family=Geist:wght@300;400;500;600;700&family=Geist+Mono:wght@400;500&display=swap" rel="stylesheet">
  <style>/* CSS completo aquí */</style>
</head>
<body>
  <!-- HTML completo aquí -->
  <script>/* JS completo aquí */</script>
</body>
</html>
```

#### Diseño UI/UX — Reglas obligatorias
**Paleta (elegir UNA de estas opciones según el tipo de negocio):**
- Dark profesional: fondo #0a0a0f, acento #6366f1, superficie #13131a, border #1e1e2e
- Dark verde tech: fondo #06080c, acento #22c55e, superficie #0f1117, border #1f2937
- Dark azul: fondo #0c0e1a, acento #3b82f6, superficie #111827, border #1f2937
- Dark premium: fondo #09090b, acento #f59e0b, superficie #111111, border #27272a

**Tipografía:**
- Fuente: Geist (UI) + Geist Mono (datos numéricos, códigos)
- Base: 16px, line-height 1.5
- Jerarquía: 28px títulos, 18px subtítulos, 14px labels, 12px captions

**Layout:**
- Sidebar fijo 240px (desktop) + contenido fluid
- Mobile: sidebar colapsable, bottom navigation o hamburger
- Breakpoint: @media (max-width: 768px)
- Spacing: múltiplos de 8px (8, 16, 24, 32, 48)

**Componentes obligatorios:**
- Toast notifications: esquina inferior derecha, auto-dismiss 3s
- Loading states: skeleton o spinner en cada fetch
- Modal con backdrop blur para confirmaciones
- Estados vacíos con mensaje descriptivo
- Botones con feedback visual (disabled durante async)

**Touch/Mobile:**
- Targets mínimos 44x44px
- touch-action: manipulation en botones
- No depender de hover para funciones críticas

**Calidad visual:**
- Cards: border-radius 12px, border 1px solid var(--border)
- Inputs: fondo superficie, border, focus con outline acento
- Tablas: header sticky, filas alternadas con opacity 0.03
- Iconos: SVG inline o Lucide, NUNCA emojis como iconos de UI
- Animaciones: 150-300ms ease, respetar prefers-reduced-motion

#### Variables CSS obligatorias
```css
:root {
  --bg: #0a0a0f;
  --surface: #13131a;
  --border: #1e1e2e;
  --accent: #6366f1;
  --accent-hover: #4f46e5;
  --text: #f8fafc;
  --text-muted: #94a3b8;
  --success: #22c55e;
  --error: #ef4444;
  --warning: #f59e0b;
  --radius: 12px;
  --font: 'Geist', sans-serif;
  --font-mono: 'Geist Mono', monospace;
}
```

#### JS — Patrón API obligatorio
```javascript
const N8N = 'https://n8n-n8n.fa2cjf.easypanel.host/webhook';
let ORG_ID = '';
let PLAN = '';

async function api(endpoint, body) {
  try {
    const res = await fetch(`${N8N}/${endpoint}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ ...body, org_id: ORG_ID })
    });
    return await res.json();
  } catch(e) {
    console.error(endpoint, e);
    toast('Error de conexión', 'error');
    return null;
  }
}

function toast(msg, type = 'success') {
  const t = document.createElement('div');
  t.className = `toast toast-${type}`;
  t.textContent = msg;
  document.getElementById('toasts').appendChild(t);
  setTimeout(() => t.remove(), 3000);
}

function sanitize(str) {
  return String(str || '').replace(/[<>"'&]/g, '').trim().slice(0, 500);
}
```

### 5. Push a GitHub
```bash
# Repo: alexanderestradaconsuegra-tech/nombre-proyecto
# Branch: main
# Archivo: index.html
```

### 6. Entregar al usuario
- Comando curl de deploy listo para copiar
- Schema SQL listo para ejecutar en psql
- Lista de endpoints n8n a crear

## Lo que NO hacer
- No usar frameworks (React, Vue, etc.)
- No crear múltiples archivos HTML
- No pedir confirmación entre pasos obvios
- No generar código parcial o con "// resto igual"
- No usar emojis como iconos de navegación
- No hardcodear colores en componentes (usar variables CSS)
