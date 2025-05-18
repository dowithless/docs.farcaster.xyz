# Solución de problemas de Hubble

## Obtener registros de Hubble

Desde la carpeta de hubble, obtén los registros con el siguiente comando:

```bash
$ ./hubble.sh logs
```

O puedes usar docker-compose directamente:

```bash
# sudo es requerido si usaste el script hubble para instalar el hub
# Puede que necesites usar `docker-compose` en lugar de `docker compose` dependiendo de tu configuración de docker
$ sudo docker compose logs -f hubble
```

## Reiniciar Hubble

Desde la carpeta de hubble, inicia y detén la instancia con los siguientes comandos:

```bash
$ ./hubble.sh down
$ ./hubble.sh up
```

## Restablecer la base de datos

La mejor manera de restablecer la base de datos es eliminar completamente el directorio `.rocks`, lo que obliga al hub a obtener la instantánea más reciente y volver a sincronizar desde allí.

```bash
$ ./hubble.sh down # Asegúrate de que el hub no esté en ejecución
$ rm -rf .rocks # asegúrate de estar en el directorio del hub
```

## Sin pares o conexiones entrantes de gossip

Si no ves ningún par o conexiones entrantes de gossip, es probable que problemas de red estén impidiendo que el hub se comunique con otros hubs.

Verifica la configuración de tu firewall y NAT para asegurarte de que los puertos 2282 (gossip) y 2283 (rpc, para sincronización) sean accesibles.

También podrías intentar inicializar el hub con un par no predeterminado añadiendo la siguiente línea a tu archivo `.env`:

```dotenv
BOOTSTRAP_NODE=/dns/hoyt.farcaster.xyz/tcp/2282
```

## ¿Está mi hub sincronizado?

Usa el [panel de grafana](/es/hubble/monitoring) para monitorear tu hub. La pestaña de Estado mostrará el porcentaje de sincronización de mensajes de tu hub en comparación con sus pares. Si es menos del 100%, intenta reiniciar el hub y espera un rato. Si persiste, abre un problema en el [repositorio del hub](https://github.com/farcasterxyz/hub-monorepo/issues/new?assignees=&labels=&projects=&template=bug_report.md&title=bug%20%28hubble%29%3A).

## Gestionando tu Peer ID

Hubble tiene un par de claves que usa para firmar mensajes peer-to-peer, que se almacena en el directorio `.hub` como un archivo `...._id.protobuf`. El nombre del archivo contiene la clave pública o Peer ID, mientras que el contenido contiene la clave privada.
