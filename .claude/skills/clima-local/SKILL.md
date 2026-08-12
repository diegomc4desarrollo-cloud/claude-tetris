---
name: clima-local
description: Consulta el tiempo y la temperatura actual de una ciudad mediante búsqueda web en tiempo real. Úsala siempre que el usuario pregunte por el clima, el tiempo, la temperatura, si va a llover o nevar, o las condiciones meteorológicas de su ciudad ("mi ciudad actual") o de cualquier otra localidad — por ejemplo "qué temperatura hace ahora", "cómo está el tiempo en Valencia", "va a llover esta tarde".
---

# Clima local

Da al usuario la temperatura y las condiciones meteorológicas actuales de una ciudad. El conocimiento propio sobre el clima está siempre desactualizado, así que nunca respondas de memoria: usa siempre la tool `WebSearch` para obtener el dato en tiempo real.

## 1. Determinar la ciudad

- Si el usuario nombra una ciudad explícitamente, usa esa.
- Si dice "mi ciudad", "mi ciudad actual" o "aquí" sin más contexto, usa la ciudad que el propio usuario haya mencionado antes en la conversación (o en memoria/CLAUDE.md si existe esa información).
- Si no hay ninguna pista sobre la ciudad, pregúntasela antes de buscar nada.

## 2. Buscar el dato

Llama a `WebSearch` con una query del tipo:

```
temperatura actual <ciudad> <país si aporta precisión> ahora
```

Si el primer resultado no trae una hora reciente o los datos parecen contradictorios entre fuentes, prioriza la fuente con la hora más reciente indicada.

## 3. Qué reportar

Responde en español, en 1-3 frases, directo y sin rodeos:

- Temperatura actual en °C.
- Condición general (soleado, nublado, lluvia, etc.).
- Viento, si el resultado lo incluye.
- Opcional: máxima/mínima prevista para el día, si es relevante.

`WebSearch` exige incluir una sección `Sources:` con los enlaces en markdown — no la omitas.

## 4. Comprobaciones repetidas

Si el usuario pide revisar el clima de forma periódica ("cada minuto", "cada hora", "avísame cuando..."), no repitas tú mismo la consulta en bucle: sugiérele usar `/loop <intervalo> <qué comprobar>` para programarlo correctamente.
