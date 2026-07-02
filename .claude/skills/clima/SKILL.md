---
name: clima
description: Obtiene el clima actual de una ciudad usando wttr.in (curl, sin API key).
  Úsala cuando el usuario pida el clima o el tiempo de un lugar, p. ej. "clima Madrid",
  "qué tiempo hace en Tokio", "/clima Barcelona". Si no se da ciudad, autodetecta por IP.
---

# Clima

Obtiene el clima actual de una ciudad (o de la ubicación detectada por IP) usando
el servicio gratuito `wttr.in` vía `curl`. No requiere API key ni configuración.

## Pasos

1. **Determinar la ciudad** a partir del argumento o de la petición del usuario.
   - Reemplaza espacios por `+` para la URL (p. ej. `New York` → `New+York`).
   - Si el usuario no indica ninguna ciudad, omite el segmento de ruta para que
     `wttr.in` autodetecte la ubicación por IP.

2. **Ejecutar** el resumen actual en una línea, en español y unidades métricas:

   ```bash
   curl -s "https://wttr.in/<CIUDAD>?format=%l:+%c+%t+(sensaci%C3%B3n+%f),+viento+%w,+humedad+%h&lang=es&m"
   ```

   Sin ciudad (autodetección por IP):

   ```bash
   curl -s "https://wttr.in/?format=%l:+%c+%t+(sensaci%C3%B3n+%f),+viento+%w,+humedad+%h&lang=es&m"
   ```

   Significado de los campos del formato:
   - `%l` lugar
   - `%c` condición (emoji)
   - `%t` temperatura
   - `%f` sensación térmica
   - `%w` viento
   - `%h` humedad
   - `lang=es` fuerza descripciones en español; `m` fuerza sistema métrico.

3. **Pronóstico ampliado (opcional):** si el usuario pide varios días o el reporte
   completo con gráfico ASCII, usa:

   ```bash
   curl -s "https://wttr.in/<CIUDAD>?lang=es&m"
   ```

4. **Presentar el resultado** al usuario en español, de forma breve y directa
   (no hace falta reformatear mucho, la salida de `wttr.in` ya es legible).

## Manejo de errores

- Usa siempre `curl -s` (modo silencioso) y encierra la URL entre comillas.
- Si `curl` falla, no hay red, o la respuesta viene vacía o con un mensaje de
  error (p. ej. ciudad desconocida), indícalo claramente al usuario y sugiere
  revisar el nombre de la ciudad o la conexión a internet. No inventes datos
  del clima.
- No se requiere API key ni variables de entorno para este servicio.
