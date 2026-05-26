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
