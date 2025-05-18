# Monitoreo de Hubble

Hubble incluye una configuración predeterminada de Grafana para monitorear sincronización y rendimiento.

Si utilizaste el script de instalación, abre localhost:3000 en un navegador para ver tu panel. Si usaste docker manualmente o instalaste desde el código fuente, es posible que necesites configurarlo tú mismo.

Si has configurado Hubble en un servidor remoto, establece un túnel SSH con `ssh -L 3000:localhost:3000 xyz@1.1.1.1` y luego abre localhost:3000 en un navegador para ver tus paneles.

## Configuración del monitoreo

1. Inicia grafana y statsd

```bash
docker compose up statsd grafana
```

2. Habilita el monitoreo en tu Hub configurando esto en tu `.env`

```bash
STATSD_METRICS_SERVER=statsd:8125
```

Si estás ejecutando Hubble desde el código fuente, puedes pasarlo como argumento de línea de comandos

```bash
yarn start --statsd-metrics-server 127.0.0.1:8125
```

3. Reinicia tu hub

4. Abre Grafana en un navegador en `127.0.0.1:3000`. El usuario/contraseña predeterminado es `admin`/`admin`. Necesitarás cambiar tu contraseña en el primer inicio de sesión

5. Ve a `Settings -> Datasource -> Add new data source` y selecciona `Graphite`. Configura la URL como `http://statsd:80` y haz clic en `Save & Test` para verificar que funcione

6. Ve a `Settings -> Dashboard -> Add New -> Import`, y en `Import from Panel JSON`, pega el contenido del [Panel predeterminado de Grafana](https://github.com/farcasterxyz/hub-monorepo/blob/main/apps/hubble/grafana/grafana-dashboard.json)
