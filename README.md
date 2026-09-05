# Collection Hub — Guía de estructura y despliegue

## Parte 1 — Estructura de Google Sheets

### Pestaña `Colecciones`

| Columna       | Tipo / Validación                                    |
|---------------|-------------------------------------------------------|
| `ID_Coleccion`| Texto único (ej. `COL_1`). Sin validación especial.   |
| `Nombre`      | Texto libre (ej. "Relojes").                          |
| `Icono`       | **Lista desplegable** con los nombres válidos: `watch`, `shoe`, `jersey`, `package` (o cualquier [nombre de ícono de Lucide](https://lucide.dev/icons) si querés ampliar). |
| `Descripcion` | Texto libre.                                           |
| `Color`       | Texto libre, formato hex (`#D4AF37`). Opcional: Validación de datos > "Es un color válido" no existe nativo en Sheets, así que dejalo como texto libre pero documentado. |
| `Activa`      | **Casilla de verificación** (Insertar > Casilla de verificación) — guarda `TRUE`/`FALSE` nativo, mejor que escribir "SI"/"NO" a mano. |
| `Vista_360`   | **Casilla de verificación**. Habilita el visor tipo "spin" (StockX) para los ítems de esa colección que tengan suficientes fotos — ver la sección dedicada más abajo. Hoy solo está tildada en `Sneakers`. |

### Pestaña `Slots`

| Columna        | Tipo / Validación |
|----------------|--------------------|
| `ID_Slot`      | Texto único (ej. `SLOT_1`). |
| `Nombre_Slot`  | Texto libre (ej. "Everyday"). |
| `ID_Coleccion` | **Lista desplegable desde un rango**: Datos > Validación de datos > "Lista de un rango" → `Colecciones!A2:A`. Así nunca escribís un ID de colección que no existe. |
| `Icono`        | Opcional, texto libre. |
| `Orden`        | Número entero. |

### Pestaña `Items`

| Columna              | Tipo / Validación |
|----------------------|--------------------|
| `ID_Item`             | Texto único (ej. `ITEM_1`). |
| `ID_Coleccion`        | **Lista desplegable** desde `Colecciones!A2:A` (igual que en Slots). |
| `Nombre`              | Texto libre. |
| `Marca`               | Texto libre. |
| `Subcategoria`        | Texto libre (considerá una lista fija si querés mantener consistencia, ej. Automático/Cuarzo para relojes). |
| `Edicion_Version`     | Texto libre. |
| `Talla`               | Texto libre, **opcional**. Pensada para zapatillas/camisetas (`42`, `M`, `XL`); dejala vacía en lo que no aplique (relojes). |
| `Descripcion`         | Texto libre (párrafo). Todo el texto libre del ítem va acá: datos técnicos y cualquier anécdota o historia personal, juntos. (Antes eran dos columnas separadas, `Especificaciones` y `Notas_Historia` — se unificaron, ver más abajo.) |
| `Precio`              | Número. |
| `Moneda`              | **Lista desplegable** fija: `USD`, `UYU`, `EUR` (ya viene cargada así en tu Sheet). |
| `Prioridad`           | **Lista desplegable** numérica del 1 al 5. (Antes llegaba solo hasta 3; se corrigió porque el sitio dibuja hasta 5 puntitos de prioridad — ver `prioridadDots()` en `index.html`.) |
| `Destacado`           | **Casilla de verificación** (TRUE/FALSE nativo). |
| `Estado`              | **Lista desplegable** fija: `Comprado`, `En Wishlist`, `Vendido`, `Regalado`. Los dos últimos son estados históricos — ver sección dedicada más abajo. |
| `ID_Slot_Asignado`    | **Lista desplegable** desde `Slots!A2:A`. Dejar vacío si no está equipado. |
| `URL_Imagenes`        | Texto libre, **una o más fotos separadas por coma**, en el orden en que se deben mostrar (link de Drive o cualquier URL de imagen directa). Antes era `URL_Imagen` (una sola) — ver la sección dedicada más abajo sobre cómo se decide si se muestra como galería simple o como el visor tipo "spin". |
| `Link_Referencia`     | Texto libre (URL). |
| `Etiquetas`           | Texto libre, **varias etiquetas separadas por coma** (ej. `Retro, Edicion limitada`). Sirve para clasificar una pieza en más de una dimensión además de `Subcategoria` — ver sección "Subcategoría vs. Etiquetas" más abajo. |
| `Fecha_Agregado`      | Fecha (formato de celda: Fecha). |

> **Ojo con no confundir:** `Colecciones` también tiene una columna llamada `Descripcion` (la descripción de la colección entera, ej. "Relojes automáticos y de cuarzo"). Es un campo distinto del `Descripcion` de `Items` (la descripción de una pieza puntual) — viven en pestañas separadas y el código las lee por separado, pero comparten nombre así que no te confundas si estás mirando las dos pestañas a la vez.

**Tip general:** para que las listas desplegables de `ID_Coleccion` y `ID_Slot_Asignado` se actualicen solas a medida que agregás colecciones o slots nuevos, usá rangos abiertos tipo `Colecciones!A2:A` (sin fijar la última fila) en vez de `Colecciones!A2:A50`.

---

## Parte 2 — Desplegar `Code.gs`

1. Abrí tu Google Sheet → **Extensiones → Apps Script**.
2. Reemplazá todo el contenido de `Code.gs` por el archivo que te dejo acá.
3. Guardá (Ctrl+S / Cmd+S).
4. **Importante para no romper la URL actual:**
   - Andá a **Implementar → Administrar implementaciones**.
   - Click en el ícono de lápiz (editar) de tu implementación existente.
   - En "Versión", elegí **Nueva versión**.
   - Click en **Implementar**.
   - Esto actualiza el código *sin cambiar la URL* `/exec` que ya tenés puesta en `index.html`.
   - Si en cambio usás **Implementar → Nueva implementación**, se genera una URL nueva y vas a tener que reemplazarla en `APPS_SCRIPT_URL` dentro del `index.html`.
5. Verificá que el acceso siga configurado como **"Cualquier usuario"** y "Ejecutar como": **"Yo"**.
6. Probá la URL directamente en el navegador — deberías ver un JSON con `"success": true`.

### Sobre el caché

El backend cachea la respuesta 10 minutos. Si actualizás datos en la Sheet y querés verlos al instante sin esperar, agregá `?refresh=1` al final de la URL una vez (esto no lo necesita el `index.html`, es solo para forzar un refresco manual mientras probás).

---

## Parte 3 — Desplegar `index.html` en GitHub Pages

1. Subí `index.html` a la raíz de tu repositorio (o a `/docs` si así tenés configurado Pages).
2. En el repo: **Settings → Pages** → confirmá que la fuente sea la rama/carpeta correcta.
3. La `APPS_SCRIPT_URL` ya viene con tu URL actual cargada en el código — no hace falta que la vuelvas a pegar, a menos que hayas creado una implementación *nueva* (ver nota arriba).
4. Esperá 1-2 minutos y abrí la URL de GitHub Pages.

---

## Fotos por ítem: galería simple o visor "spin" (estilo StockX)

`URL_Imagenes` acepta una o más fotos separadas por coma. Según cuántas fotos reales tenga un ítem — y si su colección tiene `Vista_360` tildado — la página elige automáticamente uno de dos modos, sin que tengas que configurar nada por ítem:

- **Galería simple** (siempre disponible, cualquier colección): con 1 sola foto se ve estática; con 2 o más, aparecen flechas y puntitos abajo para pasar de una a otra. Es el modo por defecto.
- **Visor "spin"** (solo si la colección tiene `Vista_360 = TRUE` **y** el ítem tiene **9 fotos o más** en `URL_Imagenes`): arrastrás sobre una barra y vas girando la pieza, como en StockX.

**Por qué 9 como mínimo:** con menos fotos el giro se ve entrecortado en vez de fluido, así que directamente no se activa — esos ítems se muestran con la galería simple aunque su colección tenga `Vista_360` prendido.

**Sobre las 36 posiciones:** StockX usa 36 fotos por producto (una cada 10°). Este visor reserva esas mismas 36 posiciones, pero no hace falta que tengas las 36: tus fotos reales se reparten parejas a lo largo de esas posiciones (la primera foto siempre cae en el extremo izquierdo del slider, la última en el extremo derecho), y los huecos que quedan sin foto se muestran en blanco — así el slider nunca se rompe ni pega saltos raros, tengas 9, 20 o 36 fotos cargadas.

**El orden importa:** las fotos se muestran en el mismo orden en que las escribas en la celda, separadas por coma. Para que el "giro" tenga sentido tenés que sacarlas girando la zapatilla de a poco (mismo ángulo entre foto y foto, misma luz, misma distancia) y pegarlas en ese orden — **arrancando siempre por la foto de perfil/costado**, porque esa es la que va a quedar en el extremo izquierdo del slider (igual que en StockX).

**Por qué está atado a `Vista_360` y no directamente a "Sneakers":** así lo pediste — para no dejarlo *hardcodeado* a una colección específica en el código. Extenderlo a Relojes o Camisetas el día de mañana es tildar esa casilla en la fila correspondiente de `Colecciones`; no hay que tocar `Code.gs` ni `index.html` para nada.

---

## Subcategoría vs. Etiquetas — cómo clasificar una pieza en más de una dimensión

`Subcategoria` es un **único valor** por ítem. Dentro de la vista Vitrina, cuando una colección no tiene Slots definidos (como pasa hoy con `Camisetas`), la página arma automáticamente chips de filtro con los valores únicos de `Subcategoria` que encuentra en esa colección. Eso la hace ideal para una clasificación de "una sola respuesta posible", tipo el deporte de la camiseta (`Futbol`, `Basquetbol`, `NFL`...).

El problema aparece cuando querés cruzar esa clasificación con otra que no es excluyente — por ejemplo "esta es una camiseta de fútbol *y* es retro". Si escribís eso todo junto en `Subcategoria` (ej. `"Futbol, Retro"`), cada combinación distinta se convierte en su propio chip suelto (`"Futbol, Retro"`, `"Basquetbol, Retro"`, `"Futbol, Moderna"`...) y nunca vas a poder filtrar "todas las retro sin importar el deporte" con un solo click.

Para resolver justamente ese caso, se agregó la columna `Etiquetas`: es una segunda dimensión, no excluyente, donde podés poner **una o varias etiquetas separadas por coma** por ítem (`Retro`, `Retro, Edicion limitada`, etc.). El sitio ahora:

- Junta todas las etiquetas usadas y las ofrece como un filtro desplegable más ("Etiqueta") en las pestañas **Grid** y **Wishlist**, combinable con Colección/Estado/Marca. Así podés elegir Colección = Camisetas + Etiqueta = Retro y ver todas las retro sin importar el deporte.
- Las incluye en la búsqueda global (arriba a la derecha).
- Las muestra como pastillas clickeables dentro del modal de detalle de cada ítem — tocarlas te lleva directo al filtro de esa etiqueta.

En resumen: **`Subcategoria` para la clasificación principal y excluyente** (deporte, tipo de movimiento del reloj, etc.), **`Etiquetas` para todo lo que se pueda combinar libremente** (Retro, Edición limitada, Firmada, Coleccionable, etc.). No hace falta que todos los ítems tengan `Etiquetas`; si la dejás vacía, simplemente no aparece en ningún filtro de etiqueta.

---

## Vendido / Regalado — cómo llevar el historial sin ensuciar la vista actual

Antes `Estado` solo distinguía `Comprado` de `En Wishlist`. El problema: el día que vendés o regalás una pieza, la única forma de "sacarla de encima" era borrar la fila — y ahí perdés el registro de que alguna vez la tuviste.

Ahora `Estado` acepta también `Vendido` y `Regalado`. La página los trata como **estados históricos**:

- No aparecen en la Vitrina (ni ocupan un Slot — si vendiste el reloj que tenías en "Everyday", ese slot vuelve a mostrarse "Sin equipar").
- No aparecen en Grid ni en el panel de estadísticas principal por defecto, para no mezclarse con tu colección actual.
- Siguen estando ahí: elegís `Estado = Vendido` (o `Regalado`) en el filtro de la pestaña Grid y los ves normalmente, con su propio color de badge para distinguirlos de un vistazo.
- El panel de estadísticas ahora tiene un quinto número, "Vendidas / regaladas", separado del resto.

## Talla — para lo que sí la necesita

Se agregó la columna `Talla` en `Items`. Es opcional y pensada sobre todo para `Sneakers` y `Camisetas` (`42`, `M`, `L`); en `Relojes` simplemente la dejás vacía. Aparece junto a la Marca en la tarjeta y en el modal de detalle, pero solo cuando el ítem la tiene cargada — si está vacía, no se muestra nada raro ni un guion suelto.

## Compartir una vista filtrada

Antes solo se podía compartir el link de un ítem puntual (`#item-ID`). Ahora los filtros de las pestañas Grid/Wishlist (Colección, Estado, Marca, Etiqueta) también quedan reflejados en la URL de la página automáticamente, así que:

- Cualquier link que copies de la barra de direcciones mientras estás filtrando ya sirve para compartir esa vista tal cual.
- Además hay un botón dedicado, **"Compartir vista"**, en la barra de filtros, que copia ese link al portapapeles con un solo click.
- Al abrir un link compartido, la página arranca directamente en la pestaña y con los filtros que tenía quien lo mandó (ej. `?tab=grid&col=COL_3&etiqueta=Retro` te abre directo en "todas las camisetas retro").

Esto es independiente del deep link de un ítem (`#item-ID`), que se sigue usando para compartir una pieza puntual desde el modal — los dos mecanismos conviven sin pisarse.

---

## Qué podés tocar en la Sheet sin romper nada (y qué no)

El backend (`Code.gs`) lee cada pestaña por **nombre exacto de columna**, no por posición. Eso te da bastante libertad:

**Seguro, no rompe nada:**
- Agregar filas nuevas en cualquier momento (ítems, slots, colecciones).
- Reordenar columnas dentro de una pestaña.
- Editar cualquier valor existente (precio, marca, nombre, descripción, color, prioridad, estado, etc.).
- Agregar una colección nueva sin Slots — automáticamente se muestra como grid filtrable por Subcategoría en vez de vitrina con slots.
- Agregar la columna `Activa` (casilla de verificación) en `Colecciones` si querés poder ocultar una colección vieja de la Vitrina sin borrar sus datos. Si no la agregás, el backend asume que todas las colecciones están activas.
- Agregar columnas nuevas propias para tu uso personal (ej. una nota interna) — el sitio simplemente las va a ignorar hasta que alguien actualice el código para leerlas.

**Rompe cosas — evitalo:**
- Renombrar las pestañas `Items`, `Slots` o `Colecciones` (el nombre se busca exacto y sensible a mayúsculas). Si lo hacés, la página va a mostrar un error de "no se encontró la pestaña".
- Renombrar un encabezado de columna que el código ya usa (ej. cambiar `ID_Coleccion` por `IdColeccion`). El dato no desaparece de la Sheet, pero el sitio deja de leerlo y lo trata como vacío.
- Dejar `ID_Item`, `ID_Coleccion` o `ID_Slot` en blanco — esa fila se descarta silenciosamente (se filtra por tener ID vacío).
- Usar el mismo ID para dos colecciones, slots o ítems distintos — puede generar cruces raros de datos entre ellos.

---

## Historial de cambios en `Code.gs` (versiones de caché)

- **v2 → v3:** se agregó `Notas_Historia` al payload (existía en la Sheet y en el modal, pero `normalizeItems()` no la pasaba — quedaba en el camino) y se sumó `Etiquetas`.
- **v3 → v4:** `Especificaciones` y `Notas_Historia` se unificaron en un solo campo, `Descripcion`. `normalizeItems()` ahora devuelve `Descripcion` en vez de esos dos campos, y el modal muestra una sola sección "Descripción" en vez de "Especificaciones" + "Notas".
- **v4 → v5:** se agregó `Talla`, y `Estado` ahora reconoce `Vendido` y `Regalado` además de `Comprado`/`En Wishlist` (nueva función `normalizeEstado()`).
- **v5 → v6:** `URL_Imagen` (una foto) pasó a `URL_Imagenes` (varias, separadas por coma). Se agregó `Vista_360` al payload de `Colecciones`, que habilita el visor "spin" para los ítems con 9+ fotos de esa colección.

Cada vez que cambia la forma del payload conviene subir la versión de `CACHE_KEY` (ya está hecho), así al desplegar no te sirve por hasta 10 minutos una respuesta vieja con la forma anterior.

## Ajustes hechos en `Mi_Coleccion_App.xlsx`

- Se agregó la columna `Activa` en `Colecciones` (casilla TRUE/FALSE), con las 3 colecciones existentes marcadas `TRUE`.
- La lista desplegable de `Icono` en `Colecciones` tenía `"default"` como opción, que **no es un ícono válido de Lucide** — el sitio lo hubiese mostrado en blanco. Se reemplazó por `package` (que es justamente el ícono de respaldo que usa el código cuando `Icono` está vacío).
- La pestaña `Slots` no tenía ninguna validación en `ID_Coleccion` (a diferencia de `Items`, que sí valida contra `Colecciones!A2:A`). Se agregó la misma lista desplegable para evitar tipeos que generen slots huérfanos.
- El desplegable de `Prioridad` en `Items` llegaba solo hasta `3`, pero el sitio dibuja hasta 5 puntitos de prioridad. Se amplió a `1,2,3,4,5`.
- Se agregó la columna `Etiquetas` en `Items` (con un comentario en el encabezado recordando el formato "separado por comas") — quedó vacía en tus ítems para que cargues los valores que tengan sentido para vos.
- Se fusionaron `Especificaciones` + `Notas_Historia` en una sola columna, `Descripcion`, conservando el texto que ya tenías en las dos (unido en un solo párrafo por ítem).
- Se agregó la columna `Talla` (opcional, vacía en tus 3 relojes actuales — no aplica).
- La lista de `Estado` ahora incluye `Vendido` y `Regalado` además de `Comprado`/`En Wishlist`.
- Se corrigió un desfase: al agregar `Talla` la vez pasada, las validaciones de Moneda/Prioridad/Destacado habían quedado pegadas a las columnas viejas (un lugar corrido) — eso era lo que te tiraba "no válido" en Precio/Moneda/Prioridad. Ya están realineadas y auditadas contra el encabezado real de cada columna.
- Se renombró `URL_Imagen` a `URL_Imagenes` (ahora acepta varias fotos separadas por coma).
- Se agregó la columna `Vista_360` en `Colecciones` (casilla TRUE/FALSE), tildada solo en `Sneakers` por ahora.
- Se fijó la primera fila (`freeze panes`) en las tres pestañas para que los encabezados queden visibles al scrollear.

---

## Qué cambió respecto a la versión anterior

- El backend ahora responde `{ success, data: { items, slots, colecciones }, timestamp }` en vez de los campos sueltos de antes — por eso hace falta actualizar **ambos** archivos juntos.
- El frontend pasó de secciones apiladas a **tabs** (Vitrina / Grid / Wishlist) con un panel de estadísticas colapsable arriba.
- Se agregó un modal de detalle por ítem con **deep link** (`#item-ID_DEL_ITEM`) — podés compartir el link directo a una pieza.
- La búsqueda ahora es difusa (tolera errores de tipeo y tildes) usando Fuse.js.
- Los filtros de Grid/Wishlist ahora muestran una etiqueta arriba de cada casillero (en vez de texto adentro del select) y arrancan vacíos. El de "Ordenar" ahora tiene direcciones separadas para Fecha y Precio (más reciente/antigua, mayor/menor a menor/mayor) — esto es solo un cambio de `index.html`, no requiere tocar la Sheet ni `Code.gs`.
- El hero de la portada tiene un elemento visual a la derecha (un "orbe" por cada colección activa, con su color e ícono real) para que no quede vacío en pantallas anchas.
