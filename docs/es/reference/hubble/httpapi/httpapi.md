# API HTTP

Hubble sirve una API HTTP en el puerto 2281 por defecto.

## Uso de la API

La API puede ser llamada desde cualquier lenguaje de programación o navegador realizando una solicitud HTTP normal.

**Ver las respuestas de la API en un navegador**

Simplemente abre la URL en un navegador

```url
http://127.0.0.1:2281/v1/castsByFid?fid=2
```

**Llamar a la API usando curl**

```bash
curl http://127.0.0.1:2281/v1/castsByFid?fid=2
```

**Llamar a la API desde Javascript, usando la librería axios**

```Javascript
import axios from "axios";

const fid = 2;
const server = "http://127.0.0.1:2281";

try {
    const response = await axios.get(`${server}/v1/castsByFid?fid=${fid}`);

    console.log(`La API devolvió el estado HTTP ${response.status}`);
    console.log(`El texto del primer Cast es ${response.messages[0].data.castAddBody.text}`);
} catch (e) {
    // Manejar errores
    console.log(response);
}
```

**Especificación OpenAPI**

Se proporciona una especificación OpenAPI para la API REST de Hubble. Puede consultarse [aquí](https://redocly.github.io/redoc/?url=https://raw.githubusercontent.com/farcasterxyz/hub-monorepo/main/packages/hub-nodejs/spec.yaml).

## Codificación de respuestas

Las respuestas de la API están codificadas como `application/json`, y pueden ser parseadas como objetos JSON normales.

1. Hashes, direcciones ETH, claves, etc... están codificados como cadenas hexadecimales que comienzan con `0x`
2. Firmas y otros campos binarios están codificados en base64
3. Las constantes están codificadas como sus tipos de cadena. Por ejemplo, el `hashScheme` se codifica como `HASH_SCHEME_BLAKE3` que equivale a `HASH_SCHEME_BLAKE3 = 1` del esquema protobuf.

## Marcas de tiempo

Los mensajes contienen marcas de tiempo que son segundos desde el Epoch de Farcaster, que comenzó el 1 de enero de 2021 00:00:00 UTC.

## Paginación

La mayoría de los endpoints soportan paginación para obtener un gran número de respuestas.

**Parámetros de consulta de paginación**

| Parámetro | Descripción                                                                                                                                        | Ejemplo                             |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| pageSize  | Número máximo de mensajes a devolver en una sola respuesta                                                                                         | `pageSize=100`                      |
| reverse   | Invertir el orden de clasificación, devolviendo los mensajes más recientes primero                                                                 | `reverse=1`                         |
| pageToken | El token de página devuelto por la consulta anterior, para obtener la siguiente página. Si este parámetro está vacío, se obtiene la primera página | `pageToken=AuzO1V0Dta...fStlynsGWT` |

El `nextPageToken` devuelto está vacío si no hay más páginas para devolver.

Los parámetros de consulta de paginación pueden combinarse con otros parámetros de consulta soportados por el endpoint. Por ejemplo, `/v1/casts?fid=2&pageSize=3`.

**Ejemplo**

Obtener todos los casts por FID `2`, obteniendo hasta 3 casts por página

```bash
# Obtener primera página
http://127.0.0.1:2281/v1/castsByFid?fid=2&pageSize=3

# Obtener siguiente página. El pageToken proviene de la respuesta anterior(`response.nextPageToken`)
http://127.0.0.1:2281/v1/castsByFid?fid=2&pageSize=3&pageToken=AuzO1V0DtaItCwwa10X6YsfStlynsGWT
```

**Ejemplo en Javascript**

```Javascript
import axios from "axios";

const fid = 2;
const server = "http://127.0.0.1:2281";

let nextPageToken = "";
do {
    const response = await axios.get(`${server}/v1/castsByFid?fid=${fid}&pageSize=100&nextPageToken=${nextPageToken}`);
    // Procesar respuesta....
    nextPageToken = response.nextPageToken;
} while (nextPageToken !== "")
```

## Manejo de errores

Si hay un error en la API, el código de estado HTTP se establece en `400` o `500` según corresponda. La respuesta es un objeto JSON con los campos `detail`, `errCode` y `metadata` para identificar y depurar los errores.

**Ejemplo**

```bash
$ curl "http://127.0.0.1:2281/v1/castById?fid=invalid"
{
  "errCode": "bad_request.validation_failure",
  "presentable": false,
  "name": "HubError",
  "code": 3,
  "details": "fid debe ser un entero",
  "metadata": {
    "errcode": [
      "bad_request.validation_failure",
    ],
  },
}
```

## CORS

Puedes establecer un encabezado CORS personalizado en el servidor HTTP usando el parámetro `--http-cors-origin` al ejecutar tu instancia de Hubble. Establecer esto a `*` permitirá solicitudes desde cualquier origen.

## Limitaciones

La API HTTP actualmente no soporta ninguna de las APIs de Sincronización que están disponibles en la versión gRPC. Cuando los Hubs se sincronizan entre sí, usarán las APIs gRPC en lugar de las APIs HTTP.
