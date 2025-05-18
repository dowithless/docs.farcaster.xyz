# Migración a snapchain

[Snapchain](https://github.com/farcasterxyz/snapchain) es una implementación más escalable del protocolo Farcaster. Para interactuar con snapchain, puedes operar tu propio nodo de lectura.

## Ejecutando un nodo

```bash
mkdir snapchain
wget https://raw.githubusercontent.com/farcasterxyz/snapchain/refs/heads/main/docker-compose.mainnet.yml -O docker-compose.yml
docker compose up # -d para ejecutar en segundo plano
```

Nota: el puerto HTTP predeterminado es `3381`, el puerto de gossip predeterminado es `3382` y el puerto gRPC predeterminado es `3383`. Es posible que necesites abrir estos puertos.

## Lectura desde snapchain

Las APIs de lectura son totalmente compatibles con los hubs, por lo que no se requiere migración. Consulta la [documentación existente](https://docs.farcaster.xyz/developers/guides/querying/fetch-casts) para leer datos mediante la biblioteca cliente.

Vía HTTP una vez que tengas un nodo en ejecución:

```bash
curl http://localhost:3381/v1/info
```

Vía gRPC:

```bash
git clone git@github.com:farcasterxyz/snapchain.git
cd snapchain
grpcurl -plaintext -proto src/proto/rpc.proto -import-path src/proto localhost:3383 HubService/GetInfo
```

Si estás usando Shuttle, funcionará igual que antes. Solo necesitas apuntarlo al nodo de snapchain.

## Escritura en snapchain

Las APIs de escritura también son compatibles con los hubs, con algunas advertencias (ver más abajo).

Para escribir en snapchain, debes ejecutar un nodo y enviar los mensajes directamente a él. Consulta la [documentación existente](https://docs.farcaster.xyz/developers/guides/writing/submit-messages) para escribir datos mediante la biblioteca cliente. Nota: **DEBES** usar la versión `0.16` o posterior de la biblioteca `hub-nodejs`.

### Consideraciones

- Si no estás usando los constructores de la biblioteca `hub-nodejs`, asegúrate de rellenar `dataBytes` en lugar de `data` en cada mensaje, así:

  ```ts
  if (message.dataBytes === undefined) {
    message.dataBytes = protobufs.MessageData.encode(message.data).finish();
    message.data = undefined;
  }
  ```

- Algunos mensajes de error devueltos por `submitMessage` son diferentes en Snapchain que en Hubs.
- Los envíos a Snapchain son de mejor esfuerzo. Es posible que `submitMessage` tenga éxito pero el mensaje no se incluya en un bloque. Sigue [este issue](https://github.com/farcasterxyz/snapchain/issues/353) para conocer el plan de proporcionar retroalimentación a los clientes cuando un mensaje aceptado en el mempool no pueda incluirse en un bloque.

## Testnet

Existe un testnet para snapchain. Puedes ejecutar un nodo contra él usando este archivo docker compose:

```bash
mkdir snap_test
wget https://raw.githubusercontent.com/farcasterxyz/snapchain/refs/heads/main/docker-compose.testnet.yml -O docker-compose.yml
docker compose up # -d para ejecutar en segundo plano
```

Nota: el testnet es inestable y se reiniciará periódicamente.
