# Plan: Landing Page AgendaOS

## Cuándo usar este plan
Cuando se pide crear o actualizar la landing page de AgendaOS (agendaos.pro).

## Contexto
- URL: https://agendaos.pro
- Repo: alexanderestradaconsuegra-tech/agendaos-landing → index.html (o repo existente)
- Deploy: EasyPanel mercado-saas/landing
- CTA principal: wa.me/56973675754

## Paleta
```css
:root {
  --bg: #06080c;
  --surface: #0d1117;
  --border: #1a2332;
  --accent: #22c55e;
  --accent-hover: #16a34a;
  --accent-glow: rgba(34,197,94,0.15);
  --text: #f0fdf4;
  --text-muted: #6b7280;
  --radius: 14px;
  --font: 'Geist', sans-serif;
  --font-mono: 'Geist Mono', monospace;
}
```

## Estructura de secciones (en orden)

### 1. Hero
- Headline impactante (problema que resuelve)
- Subheadline (beneficio principal)
- CTA primario → WhatsApp
- Visual: mockup del dashboard o animación CSS
- Badge social proof: "X negocios activos"

### 2. Problema / Solución
- Antes (sin AgendaOS): caos, reservas por WhatsApp manual
- Después (con AgendaOS): automatizado, profesional

### 3. Features principales (3-4 cards)
- Agenda online 24/7
- Recordatorios automáticos WhatsApp
- Panel de control
- Pagos integrados

### 4. Cómo funciona (3 pasos)
- Step 1: Te registras
- Step 2: Configuras tu negocio
- Step 3: Tus clientes agendan solos

### 5. Pricing (2-3 planes)
- Básico: $29.990/mes
- Pro: $49.990/mes
- Elite (con IA): $79.990/mes
- CTA cada plan → WhatsApp

### 6. Rubros objetivo (grid de iconos)
Barberías, Salones, Médicos, Psicólogos, Nutricionistas, Spas, Dentistas, Veterinarias

### 7. CTA Final
- Headline urgencia
- Botón WhatsApp grande
- "Configuración en menos de 24 horas"

### 8. Footer
- Logo + descripción
- Links: Inicio, Features, Precios
- WhatsApp + email

## Reglas de diseño UI/UX para landing

### Visual
- Dark-only, NUNCA toggle light mode
- Animaciones sutiles: fade-in al scroll (IntersectionObserver)
- Duración animaciones: 300-500ms ease-out
- Glassmorphism en cards: backdrop-filter blur(12px), border con opacity
- Gradientes: solo en hero y CTA, no en todo el layout
- Glow en acento: box-shadow 0 0 40px var(--accent-glow)

### Performance
- Sin librerías externas (solo Google Fonts)
- Imágenes: si hay mockups usar CSS puro o SVG
- Lazy load en secciones below fold

### Mobile-first obligatorio
- Hero: stack vertical en mobile
- Pricing: cards en scroll horizontal o stack
- Nav: hamburger menu en mobile
- CTA flotante en mobile (botón WhatsApp fijo bottom)

### Elementos obligatorios
- Meta tags SEO completos
- Open Graph para WhatsApp/redes
- Schema.org LocalBusiness
- Botón WhatsApp flotante en mobile
- Smooth scroll entre secciones
- Toast o modal de confirmación en forms

## Patrón de animación scroll
```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.isIntersecting) e.target.classList.add('visible');
  });
}, { threshold: 0.1 });

document.querySelectorAll('.animate').forEach(el => observer.observe(el));
```

```css
.animate { opacity: 0; transform: translateY(20px); transition: all 0.4s ease-out; }
.animate.visible { opacity: 1; transform: translateY(0); }
```

## Deploy
```bash
curl -L "https://raw.githubusercontent.com/alexanderestradaconsuegra-tech/REPO/main/index.html" \
  -o /usr/share/nginx/html/index.html && echo "LISTO"
```

## Lo que NO hacer
- No usar Bootstrap ni Tailwind CDN
- No poner formularios de registro (todo va a WhatsApp)
- No hacer la landing demasiado larga (máximo 7 secciones)
- No usar stock photos genéricas
- No poner precios sin el CTA de WhatsApp junto
