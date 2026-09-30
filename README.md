# ⏱ Temporizador de tareas

App para medir el tiempo dedicado a cada **cliente › proyecto › tarea**.
Gratis: el código está en GitHub, la web en GitHub Pages y los datos en Firebase (Google).

## Qué hace (v0.5.1)

- Iniciar/parar el temporizador en tiempo real, **sincronizado entre dispositivos** (lo inicias en el móvil y lo paras en el PC).
- Añadir, editar y borrar registros de tiempo a mano.
- Clientes, proyectos y tareas: crear, renombrar, archivar y borrar. También se pueden crear al vuelo desde los desplegables ("+ Nuevo…").
- Precio €/h en cascada: **Tarea → Proyecto → Cliente**. Vacío = hereda; 0 = no facturable. Cada registro guarda su precio, y al cambiar una tarifa la app pregunta si aplicarla también a lo ya registrado.
- Temporizador **sin asignar**: arráncalo sin elegir nada y complétalo después.
- Aviso si ya hay un temporizador en marcha, y revisión de la hora de fin si lleva más de 10 h (temporizador olvidado).
- Informes por periodo con horas e importe, y exportación a CSV (se abre en Excel).
- Al abrir solo carga los **últimos 3 meses**. El historial anterior se descarga automáticamente cuando lo consultas (Registros o Informes con fechas antiguas) o cuando exportas. Así gasta muy pocas lecturas de la base de datos.
- Funciona sin conexión: guarda los cambios y los sube cuando vuelve la red.
- Copia extra opcional: exportar/importar JSON.

## Dónde vive cada cosa

| Qué | Dónde | Coste |
|---|---|---|
| **El código** | Este repositorio de GitHub | Gratis |
| **La web** (tu URL) | GitHub Pages: `https://TU_USUARIO.github.io/temporizador/` | Gratis |
| **Tus datos** | Firebase Firestore (base de datos en la nube de Google), vinculados a tu cuenta de Google | Gratis (plan Spark) |

- Cambiar o mejorar la app (el código) **no toca tus datos**: están en Firebase, no en GitHub.
- Varios usuarios: cada persona entra con su cuenta de Google y solo puede leer y escribir sus propios datos. Lo garantizan las reglas de `firestore.rules`, que se aplican en el servidor de Google.
- El plan gratuito de Firebase no caduca ni se pausa por falta de uso. Sus límites (1 GB y 20.000 escrituras diarias) quedan muy lejos de lo que gasta un temporizador personal. No hace falta poner tarjeta.

## Estructura de los datos

```
users/{tu-id}                 → clientes, proyectos, tareas, temporizador en marcha
users/{tu-id}/entries/{id}    → un documento por cada registro de tiempo
```

---

## Puesta en marcha (una sola vez, unos 15 minutos)

### A. Firebase (la base de datos)

1. Entra en <https://console.firebase.google.com> con tu cuenta de Google y pulsa **Crear proyecto**. Llámalo, por ejemplo, `temporizador`. Google Analytics no hace falta.
2. **Authentication** (menú *Compilación / Build*) → *Comenzar* → *Método de acceso* → **Google** → *Habilitar* → *Guardar*.
3. **Firestore Database** → *Crear base de datos* → ubicación en Europa (por ejemplo, `europe-southwest1`, que es Madrid) → *modo producción*.
4. En Firestore, pestaña **Reglas**: borra lo que haya, pega el contenido de `firestore.rules` y pulsa *Publicar*.
   Cualquier cuenta de Google puede entrar, pero cada una solo ve sus propios datos. Si prefieres limitarlo a una lista de personas, sigue la opción del final del archivo de reglas.
5. En **Authentication → Configuración → Dominios autorizados**, añade `TU_USUARIO.github.io`.
6. En **⚙ Configuración del proyecto → Tus apps**, pulsa el icono web `</>` → registra la app (sin Hosting) → copia el bloque `firebaseConfig` → pégalo en `firebase-config.js`.

### B. GitHub (el código y la web)

1. Crea un repositorio vacío llamado `temporizador` en <https://github.com/new>.
2. Sube el código desde la carpeta del proyecto:
   ```bash
   git add firebase-config.js
   git commit -m "Configura mi proyecto Firebase"
   git remote add origin https://github.com/TU_USUARIO/temporizador.git
   git push -u origin main
   ```
   (Sin terminal: GitHub Desktop → *Add existing repository* → *Publish repository*.)
3. **Settings → Pages → Deploy from a branch → `main` / `(root)` → Save**.
4. Al cabo de 1 o 2 minutos: `https://TU_USUARIO.github.io/temporizador/` → *Entrar con Google*. ¡Listo!

> Probar en tu ordenador: la app no funciona abriendo el archivo con doble clic (los navegadores lo bloquean).
> Lanza `python3 -m http.server 8000` en la carpeta y abre `http://localhost:8000`.

---

## Flujo para mejorar la app (el ciclo de los commits)

```bash
git status                 # ¿qué ha cambiado?
git diff                   # ver los cambios línea a línea
git add .                  # preparar los cambios
git commit -m "Añade filtro por proyecto en informes"   # guardar una "foto" con descripción
git push                   # subir a GitHub → la web se actualiza sola en 1-2 min
```

Otros comandos útiles:
```bash
git log --oneline          # historial de versiones
git revert <id>            # deshacer un commit (crea uno nuevo que lo anula)
git checkout -b prueba     # rama para experimentar sin tocar la versión buena
```

## Regla de oro al cambiar la estructura de datos

Si una mejora cambia cómo se guardan los datos:
1. Sube `SCHEMA` en `index.html`.
2. Añade un paso en `migrate()` que convierta los datos antiguos al formato nuevo.

Antes de un cambio grande, exporta una copia JSON (pestaña *Datos*) por si acaso.

## Historial de versiones

- **0.5.1**: Grises más legibles y opción "+ Nuevo…" en los desplegables de cliente, proyecto y tarea.
- **0.5.0**: Nuevo diseño minimalista (tipografía Poppins, menú inferior en el móvil, modo oscuro automático).
- **0.4.0**: Precios por cliente/proyecto/tarea en cascada, temporizador sin asignar y aviso de temporizadores olvidados.
- **0.3.0**: Carga los últimos 3 meses al abrir y el historial bajo demanda (ahorro de lecturas).
- **0.2.2**: Multiusuario (cualquier cuenta de Google, datos aislados por usuario).
- **0.2.0**: Datos en Firebase con acceso mediante Google, sincronización en tiempo real y modo sin conexión. Ofrece subir los datos de la v0.1 guardados en el navegador.
- **0.1.0**: MVP con los datos en el navegador.
