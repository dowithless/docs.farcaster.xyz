# Conteo de registros por día

::: info Requisitos previos

- Acceso de lectura a una base de datos replicator

:::

Para contar el número de registros por día, podemos utilizar la tabla `chain_events` para consultar la cantidad de eventos [`ID_REGISTER`](/es/reference/hubble/datatypes/events#onchaineventtype) y agruparlos por día.

```sql
SELECT DATE_TRUNC('day', created_at) AS day, COUNT(*) AS count
FROM chain_events
WHERE type = 3 -- ID_REGISTER (ver página de referencia de tipos de eventos)
GROUP BY day
ORDER BY day desc;
```

Para más información sobre el esquema, consulta el [esquema del replicator](/es/reference/replicator/schema).
