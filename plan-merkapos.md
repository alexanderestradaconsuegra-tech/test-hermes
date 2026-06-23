# Plan: Merka POS — Sistema POS Minimarket

## Cuándo usar este plan
Cuando se pide agregar features, módulos o fixes a Merka POS (app.merkapos.pro).

## Contexto del proyecto
- URL: https://app.merkapos.pro
- Repo: alexanderestradaconsuegra-tech/estrada → index.html
- DB: postgres://merka_user:Merka2026@market-saas_merka-db:5432/merka
- Deploy: EasyPanel mercado-saas/estrada
- Negocios activos: default/merka2026, nya/123456, audie/1234

## Paleta de colores
```css
:root {
  --bg: #0c1a2e;
  --surface: #0f2133;
  --border: #1a3a5c;
  --accent: #0ea5e9;
  --accent-hover: #0284c7;
  --lime: #65a30d;
  --text: #f0f9ff;
  --text-muted: #94a3b8;
  --success: #22c55e;
  --error: #ef4444;
  --radius: 10px;
  --font: 'Geist', sans-serif;
  --font-mono: 'Geist Mono', monospace;
}
```

## Endpoints n8n activos
- /webhook/merka-auth — login org_id + password
- /webhook/merka-productos — CRUD productos
- /webhook/merka-ventas — registrar ventas, historial
- /webhook/merka-clientes — CRUD clientes
- /webhook/merka-agente — agente IA

## Schema de tablas principales
```sql
productos (id, org_id, nombre, precio, stock, categoria, codigo_barra, por_peso, activo)
ventas (id, org_id, total, items_json, metodo_pago, cliente_id, created_at)
clientes (id, org_id, nombre, telefono, email, rut)
```

## Módulos del sistema
1. Terminal de ventas — búsqueda productos, carrito, cobro
2. Gestión de productos — CRUD, stock, categorías
3. Clientes — CRUD, historial de compras
4. Reportes — ventas del día, semana, mes
5. Agente IA — consultas en lenguaje natural

## Pasos para agregar una feature nueva
1. Identificar si requiere nuevo endpoint n8n o usa uno existente
2. Si requiere nuevo endpoint: seguir patrón Webhook→Code→Switch→Postgres→Respond
3. Agregar la UI en index.html (sección o módulo nuevo)
4. Conectar con api() usando el endpoint correspondiente
5. Push a GitHub → dar comando curl de deploy

## Deploy
```bash
curl -L "https://raw.githubusercontent.com/alexanderestradaconsuegra-tech/estrada/main/index.html" \
  -o /usr/share/nginx/html/index.html && echo "LISTO"
```

## Features pendientes conocidas
- DTE/SII: integración Facturahoy API para boletas electrónicas
- Stock productos por peso: parsing correcto de unidades
- Persistencia de sesión entre reloads de página
