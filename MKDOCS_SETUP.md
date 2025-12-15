# MkDocs Setup Local

Para ejecutar la documentación localmente:

```bash
# 1. Instalar dependencias
pip install -r requirements.txt

# 2. Correr el servidor local
mkdocs serve

# 3. Abrir en navegador
# http://localhost:8000
```

## Cambios desde Jekyll

- ✅ **Material Theme** - Diseño moderno
- ✅ **Mejor búsqueda** - Search integrado
- ✅ **Dark mode** - Automático
- ✅ **Más rápido** - ~0.5 seg vs ~1-2 seg
- ✅ **Python** - Más fácil mantener

## Deploy

Automático con cada push a `main`:
1. GitHub Actions detecta cambios
2. Build con mkdocs
3. Deploy a Pages (~1 min)

## Estructura

```
docs/
├── index.md                # Home
├── guides/                 # Tutoriales
├── api/                    # Referencia API
```

Edit cualquier `.md` y auto-reload en navegador.
