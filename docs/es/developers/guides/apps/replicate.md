# Replicar datos de Hubble en Postgres

::: info Requisitos previos

- Instancia de Hubble ejecutándose localmente (para mejor rendimiento) o de forma remota

:::

Aunque algunas aplicaciones pueden escribirse consultando directamente a Hubble, la mayoría de aplicaciones serias necesitan acceder a los datos de una manera más estructurada.

[Shuttle](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/shuttle) es un paquete que puede utilizarse para reflejar los datos de Hubble en una base de datos Postgres, facilitando el acceso a los datos subyacentes.

Consulta la documentación para más información.
