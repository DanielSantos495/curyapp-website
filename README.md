# cury-apps-site

Sitio estático de **Cury Apps**. Contiene la landing del paraguas (`/`) y las páginas legales y de marketing por app (`/seo-analyzer/...`). Pensado para hostearse en **Cloudflare Pages** con `curyapps.com` apuntando a él.

## Estructura

```
cury-apps-site/
├── index.html                  ← Landing de Cury Apps (curyapps.com)
├── seo-analyzer/
│   ├── index.html              ← Landing de la app (curyapps.com/seo-analyzer)
│   ├── privacy.html            ← Privacy Policy (curyapps.com/seo-analyzer/privacy)
│   └── terms.html              ← Terms of Service (curyapps.com/seo-analyzer/terms)
└── assets/
    ├── styles.css              ← Estilos compartidos
    └── favicon.svg
```

> Cuando agreguemos otra app: crear `/<app-slug>/` siguiendo el mismo patrón.

## URLs finales (post-deploy)

- `https://curyapps.com/`
- `https://curyapps.com/seo-analyzer/`
- `https://curyapps.com/seo-analyzer/privacy` (o `/privacy.html` — ambas válidas en Pages)
- `https://curyapps.com/seo-analyzer/terms`

Las dos últimas son las que se pegan en el listing de Shopify (`§10 Privacy Policy URL` y `§10 Terms of Service URL` de `marketing/seo-app/listing-content.md`).

---

## Deploy paso a paso — Cloudflare Pages

Hay dos caminos. Elegí uno.

### Camino A — Direct Upload (más rápido, sin git)

> Sirve para subir hoy mismo y validar URLs. Después se puede migrar a Git si querés CI/CD.

1. Entrá al **Cloudflare Dashboard** → tu cuenta → menú izquierdo **Workers & Pages** → pestaña **Pages** → botón **Create application** → tab **Direct Upload**.
2. Project name: `cury-apps-site` (será también `cury-apps-site.pages.dev` mientras no asignes dominio).
3. **Production branch:** dejar el default (`production`).
4. **Upload folder:** seleccioná la carpeta `cury-apps-site/` completa de tu Mac (la raíz del repo, no su contenido). Cloudflare sube los archivos manteniendo la estructura.
5. **Save and Deploy.** A los segundos tenés un URL tipo `https://cury-apps-site.pages.dev` con todo funcionando.
6. Abrí ese URL y validá:
   - `/` carga la landing.
   - `/seo-analyzer/` carga la landing de la app.
   - `/seo-analyzer/privacy.html` y `/seo-analyzer/terms.html` cargan correctamente.

> Updates futuros con este método: volvés a **Pages → cury-apps-site → Create new deployment → Direct Upload** y subís la carpeta otra vez. Cloudflare reemplaza la versión.

### Camino B — Git (recomendado para iteración)

1. Crear un repo nuevo en GitHub: `cury-apps-site` (privado o público, da igual).
2. Inicializar local y subir:
   ```bash
   cd cury-apps-site
   git init
   git add .
   git commit -s -m "feat: initial cury apps site with seo-analyzer legal pages"
   git branch -M main
   git remote add origin git@github.com:<usuario>/cury-apps-site.git
   git push -u origin main
   ```
3. Cloudflare Dashboard → **Workers & Pages → Create application → Pages → Connect to Git**.
4. Autorizar GitHub, elegir `cury-apps-site`.
5. Build settings:
   - **Framework preset:** `None`.
   - **Build command:** dejar vacío.
   - **Build output directory:** dejar vacío (sirve la raíz).
   - **Root directory:** dejar vacío.
6. **Save and Deploy.** Mismo `pages.dev` URL al final.
7. A partir de ahora, cada `git push` a `main` redeploya solo.

---

## Conectar el dominio `curyapps.com`

Una vez el deploy preview en `*.pages.dev` esté OK:

1. En el proyecto de Pages → tab **Custom domains** → **Set up a custom domain**.
2. Escribí `curyapps.com` → **Continue**.
3. Cloudflare detecta que el dominio ya está en tu cuenta (Registrar). Te ofrece crear los DNS records automáticamente. Aceptar.
4. Repetir para `www.curyapps.com` y configurarlo como redirect a apex si querés (opcional). Página de Pages → **Custom domains** → **Add a domain** → `www.curyapps.com` → Cloudflare propone CNAME a la apex.
5. Esperar 1–5 min a que el cert SSL emita (`Pending` → `Active`).
6. Validar en browser:
   - `https://curyapps.com/` → landing.
   - `https://curyapps.com/seo-analyzer/privacy.html` → privacy policy.
   - `https://curyapps.com/seo-analyzer/terms.html` → terms.

> Si no aparece el cert después de 10 min, en **SSL/TLS → Overview** poner el mode en **Full**.

---

## Después del deploy: actualizar el listing

Una vez las URLs estén live, pegar en el Partner Dashboard de Shopify:

- **Privacy Policy URL:** `https://curyapps.com/seo-analyzer/privacy.html`
- **Terms of Service URL:** `https://curyapps.com/seo-analyzer/terms.html`

Y marcar `§10` del checklist en `marketing/seo-app/listing-content.md` como ✅.

---

## Edición y mantenimiento

- Las páginas son HTML puro + un CSS compartido en `assets/styles.css`. No hay build step.
- Para previsualizar local: cualquier server estático funciona, por ejemplo:
  ```bash
  cd cury-apps-site
  python3 -m http.server 4173
  # → http://localhost:4173/
  ```
- Dark mode automático via `prefers-color-scheme`. No hay toggle manual (intencional, mantiene el código simple).
- Cuando cambien las legales (V2 con AI, nuevo sub-procesador, etc.) actualizar la fecha **Effective date** y **Last updated** al inicio de la página.

---

## Notas legales importantes

- Privacy Policy y Terms son **borradores escritos por el desarrollador**, no revisados por abogado. Son suficientes para el review inicial del App Store y para una operación pequeña, pero **antes de monetizar (V2) o escalar el merchant base**, conseguí una revisión legal real, especialmente sobre:
  - Limitation of liability cap y enforceability en tu jurisdicción.
  - Cláusulas GDPR si vas a procesar PII de end-customers (no es el caso hoy).
  - Jurisdicción y arbitraje.
- Cuando V2 introduzca IA o nuevos sub-procesadores (Anthropic, OpenAI, etc.), **actualizar §7 (Third parties)** y **§8 (AI)** de Privacy y **§7 (SEO disclaimers)** de Terms antes de shipear la feature.
