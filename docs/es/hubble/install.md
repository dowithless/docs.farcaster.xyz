# Instalación

Recomendamos ejecutar Hubble en un servidor siempre activo que tenga [Docker](https://docs.docker.com/desktop/install/linux-install/) instalado.

## Requisitos

Hubble puede configurarse en menos de 30 minutos. Necesitarás una máquina con:

- 16 GB de RAM
- 4 núcleos de CPU o vCPUs
- 1TB de almacenamiento libre
- Una dirección IP pública con los puertos 2281 - 2283 expuestos
- Puntos finales RPC para Ethereum y Optimism Mainnet. (usa [Alchemy](https://www.alchemy.com/), [Infura](https://www.infura.io/), [QuickNode](https://www.quicknode.com/), o ejecuta tus propios nodos [Ethereum](https://geth.ethereum.org/docs/getting-started) & [Optimism](https://docs.optimism.io/builders/node-operators/tutorials/mainnet)).

Consulta los [tutoriales](./tutorials) para obtener instrucciones sobre cómo configurar proveedores en la nube para ejecutar Hubble.

## Instalar mediante Script

El script de instalación es la forma más sencilla de configurar Hubble.

```bash
curl -sSL https://download.thehubble.xyz/bootstrap.sh | bash
```

_Si estás usando macOS, necesitarás tener Docker instalado y en ejecución._

Hubble se instalará en `~/hubble` y se ejecutará mediante Docker en segundo plano, junto con Grafana y Prometheus para [monitoreo](monitoring.md). Si tienes problemas con el script, prueba [instalando mediante Docker](#install-via-docker).

### Actualizar Hubble

El script de Hubble crea una entrada en crontab y actualizará automáticamente el hub cada semana. Para actualizar manualmente, ejecuta:

```bash
cd ~/hubble && ./hubble.sh upgrade
```

## Instalar mediante Docker

Hubble también puede configurarse ejecutando directamente la imagen de Docker. Para hacerlo:

1. Clona el [hub-monorepo](https://github.com/farcasterxyz/hub-monorepo) localmente.
2. Desde la raíz de esta carpeta, navega a `apps/hubble`
3. Genera tu par de claves de identidad con docker compose.

```bash
docker compose run hubble yarn identity create
```

4. Crea un archivo .env en `apps/hubble` con tus puntos finales RPC de Ethereum:

```bash
# Establece esto a tu URL RPC de ETH Mainnet
ETH_MAINNET_RPC_URL=tu-URL-RPC-de-ETH-mainnet

# Establece esto a tu URL RPC de Optimism Mainnet L2
OPTIMISM_L2_RPC_URL=tu-URL-RPC-de-L2-optimism

# Establece esto a tu FID de Farcaster
HUB_OPERATOR_FID=tu-fid
```

5. Sigue las instrucciones para [conectarte a una red](./networks.md).

6. Inicia Hubble con docker compose en modo desatendido:

```bash
docker compose up hubble -d
```

Docker compose iniciará un contenedor de Hubble que expone puertos para redes y escribe datos en los directorios `.hub` y `.rocks`. Hubble ahora se sincronizará con los contratos y otras instancias de Hubble para descargar todos los mensajes en la red.

7. Para ver el estado de la sincronización y Hubble, sigue los registros

```bash
docker compose logs -f hubble
```

8. Sigue las instrucciones en las [instrucciones de monitoreo](monitoring.md) para configurar Grafana y ver el estado de tu Hub en tiempo real.

### Actualizar Hubble

Navega a `apps/hubble` en hub-monorepo y ejecuta:

```bash
git checkout main && git pull
docker compose stop && docker compose up -d --force-recreate --pull always
```

## Instalar desde el código fuente

Hubble también puede construirse y ejecutarse directamente desde el código fuente sin Docker.

#### Instalar Dependencias

Primero, asegúrate de que lo siguiente esté instalado globalmente en tu máquina:

- [Node.js 18.7+](https://nodejs.org/en/download/releases)
- [Yarn](https://classic.yarnpkg.com/lang/en/docs/install)
- [Foundry](https://book.getfoundry.sh/getting-started/installation#using-foundryup)
- [Rust](https://www.rust-lang.org/tools/install)

#### Construir

- `git clone https://github.com/farcasterxyz/hub-monorepo.git` para clonar el repositorio
- `cd hub-monorepo` para entrar al directorio
- `yarn install` para instalar dependencias
- `yarn build` para construir Hubble y sus dependencias
- `yarn test` para asegurarte de que la suite de pruebas se ejecute correctamente

#### Ejecutar Hubble

Para ejecutar los comandos de Hubble, ve a la aplicación Hubble (`cd apps/hubble`) y ejecuta los comandos `yarn`.

1. `yarn identity create` para crear una ID
2. Sigue las instrucciones para [conectarte a una red](./networks.md)
3. `yarn start --eth-mainnet-rpc-url <tu-URL-RPC-de-ETH-mainnet> --l2-rpc-url <tu-URL-RPC-de-Optimism-L2> --hub-operator-fid <tu-FID>`

### Actualizar Hubble

Para actualizar Hubble, encuentra la última [etiqueta de lanzamiento](https://github.com/farcasterxyz/hub-monorepo/releases) y haz checkout de esa versión y construye.

```bash
git fetch --tags # para obtener las últimas etiquetas
git checkout @farcaster/hubble@latest # O usa una versión específica.
yarn install && yarn build # en la carpeta raíz
```

## Ejecutar comandos

Revisa los registros para asegurarte de que tu hub se esté ejecutando correctamente:

```bash
docker compose logs -f hubble
```

Abre una shell dentro del contenedor de Hubble ejecutando:

```bash
docker compose exec hubble /bin/sh
```

## Solución de problemas

- Si actualizas desde una implementación que no usa Docker, asegúrate de que los directorios `.hub` y `.rocks` tengan permisos de escritura para todos los usuarios.

- Si actualizas desde la versión 1.3.3 o inferior, por favor establece `ETH_MAINNET_RPC_URL=tu-URL-RPC-de-ETH-mainnet` (si usas Docker) o proporciona el flag `--eth-mainnet-rpc-url` (si no usas Docker)

- Si estás cambiando tu Hub de una red a otra, necesitarás borrar el contenido de tu base de datos:

```bash
docker compose stop && docker compose run --rm hubble yarn dbreset
```

- Para descargar la imagen tú mismo, puedes ejecutar:

```bash
# Obtener la imagen más reciente
docker pull farcasterxyz/hubble:latest

# Obtener un lanzamiento específico (v1.4.0)
docker pull farcasterxyz/hubble@v1.4.0
```

- Para establecer el FID del operador del Hub
  - Si estás ejecutando mediante Docker o el script, por favor establece esto en tu archivo `.env`: `HUB_OPERATOR_FID=tu-fid`
  - Si estás ejecutando desde el código fuente `yarn start --hub-operator-fid <tu-fid>`
