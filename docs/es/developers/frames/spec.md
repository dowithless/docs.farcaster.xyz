---
title: Especificación de Frames
---

# Especificación de Frames

Los Frames son un estándar para crear experiencias interactivas y autenticadas en Farcaster, integrables en cualquier cliente de Farcaster.

Un frame es un conjunto de etiquetas `<meta>` devueltas dentro del `<head>` de una página HTML. Si una página contiene todas las propiedades requeridas de un frame, las aplicaciones de Farcaster renderizarán la página como un frame. Las etiquetas `<meta>` del frame extienden el [protocolo OpenGraph](https://ogp.me/).

Los frames pueden enlazarse entre sí para crear aplicaciones dinámicas incrustadas dentro de casts.

## Ciclo de vida de una aplicación de frame

Una aplicación de frame comienza con un frame inicial que es almacenado en caché por las aplicaciones y mostrado a los usuarios. Un frame debe tener una imagen. Puede tener botones, que al ser clickeados cargan otros frames o redirigen al usuario a sitios web externos.

![Aplicación de Frame](/assets/frame_app.png)

### Frames Iniciales

Un frame es una aplicación web HTML que reside en una URL (ej. foo.com/app) en un servidor web. Nos referiremos a este servidor web como el "servidor de frame".

El servidor de frame:

- Debe devolver un frame válido en la sección `<head>` del HTML.
- Debería devolver un `<body>` HTML válido, en caso de que el usuario haga clic en el frame en un navegador.
- No debería incluir contenido dinámico en el frame inicial, ya que es almacenado en caché por los clientes de Farcaster.
- No debería incluir una etiqueta `fc:frame:state`.

### Frames de Respuesta

Cuando un usuario hace clic en un botón de un frame, la aplicación realiza una solicitud POST al servidor de frame con una [firma de frame](#firma-de-frame) que prueba que la solicitud provino del usuario. El servidor debe responder con un nuevo frame que se envía de vuelta al usuario.

Cuando un servidor de frame recibe una solicitud POST:

- Debe responder dentro de 5 segundos.
- Debe responder con un 200 OK y otro frame, en un clic de botón `post` para indicar éxito.
- Debe responder con un 302 OK y un encabezado Location, en un clic de botón `post_redirect` para indicar éxito.
- Puede responder con un estado 4XX, encabezado `content-type: application/json` y cuerpo JSON que contenga una propiedad `message` de <= 90 caracteres para indicar un error a nivel de aplicación.
- Cualquier encabezado Location proporcionado debe contener una URL que comience con `http://` o `https://`.

### Renderizado de Frames

Un frame entra en Farcaster cuando un usuario crea un cast e incrusta la URL del frame en él. Una aplicación que desee soportar frames debe:

- Verificar todas las URLs de incrustación de casts para frames válidos.
- Si el frame es válido, renderizar el frame cuando se visualice el cast.
- Si el frame está mal formado, recurrir a tratarlo como una incrustación OpenGraph.
- Seguir el [modelo de seguridad](#protección-de-frames) del frame.

## Construcción de un frame

Un frame debe incluir propiedades requeridas y puede contener propiedades opcionales. Los frames pueden validarse utilizando la herramienta [Validador de Frames](https://warpcast.com/~/developers/frames-legacy) proporcionada por Warpcast.

### Propiedades

Una propiedad de frame es una etiqueta meta con una propiedad y un valor de contenido. Las propiedades siempre tienen el prefijo `fc:frame`.

```html
<!-- Ejemplo declarando un frame y versión soportada -->

<meta property="fc:frame" content="vNext" />
```

### Propiedades Requeridas

| Clave            | Descripción                                                                                                                                                                                                            |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fc:frame`       | Una cadena de versión de frame válida. La cadena debe ser una fecha de lanzamiento (ej. 2020-01-01) o vNext. Las aplicaciones deben ignorar versiones que no entiendan. Actualmente, la única versión válida es vNext. |
| `fc:frame:image` | Una imagen que debe tener una relación de aspecto de 1.91:1 o 1:1                                                                                                                                                      |
| `og:image`       | Una imagen que debe tener una relación de aspecto de 1.91:1. Alternativa para clientes que no soportan frames.                                                                                                         |

### Propiedades Opcionales

| Clave                           | Descripción                                                                                                                                                                                                                                                                                       |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fc:frame:post_url`             | Una cadena de 256 bytes que contiene una URL válida a la que enviar el Paquete de Firma.                                                                                                                                                                                                          |
| `fc:frame:button:$idx`          | Una cadena de 256 bytes que es la etiqueta del botón en la posición $idx. Una página puede contener de 0 a 4 botones. Si hay más de 1 botón presente, los valores idx deben estar en secuencia comenzando desde 1 (ej., 1, 2, 3). Si hay una secuencia rota (ej., 1, 2, 4), el frame es inválido. |
| `fc:frame:button:$idx:action`   | Debe ser `post`, `post_redirect`, `link`, `mint` o `tx`. Por defecto es `post` si no se especifica. Ver [Acciones de Botón](#acciones-de-botón) para detalles sobre cada acción.                                                                                                                  |
| `fc:frame:button:$idx:target`   | Una cadena de 256 bytes que determina el objetivo de la acción.                                                                                                                                                                                                                                   |
| `fc:frame:button:$idx:post_url` | Una cadena de 256 bytes que define una URL específica del botón para enviar el Paquete de Firma. Si se establece, esto anula `fc:frame:post_url`.                                                                                                                                                 |
| `fc:frame:input:text`           | Agregar esta propiedad habilita el campo de texto. El contenido es una etiqueta de 32 bytes que se muestra al usuario (ej., Ingrese un mensaje).                                                                                                                                                  |
| `fc:frame:image:aspect_ratio`   | Debe ser `1.91:1` o `1:1`. Por defecto es `1.91:1`                                                                                                                                                                                                                                                |
| `fc:frame:state`                | Una cadena que contiene estado serializado (ej. JSON) pasado al servidor de frame. Puede ser de hasta 4096 bytes.                                                                                                                                                                                 |

## Acciones de Botón

### `post`

```html
<meta name="fc:frame:post_url" content="https://frame.example.com/start" />
<meta name="fc:frame:button:1" content="Start" />
```

La acción `post` envía una solicitud HTTP POST a la URL `post_url` del frame o botón. Este es el tipo de botón por defecto.

El servidor de frame recibe un Paquete de Firma en el cuerpo POST, que incluye información sobre qué botón se hizo clic, entrada de texto y el contexto del cast. El servidor de frame debe responder con un 200 OK y otro frame.

### `post_redirect`

```html
<meta name="fc:frame:post_url" content="https://frame.example.com/redirect" />
<meta name="fc:frame:button:1" content="Redirect" />
<meta name="fc:frame:button:1:action" content="post_redirect" />
```

La acción `post_redirect` envía una solicitud HTTP POST a la URL `post_url` del frame o botón. Puedes usar esta acción para redirigir a una URL basada en el estado del frame o entrada del usuario.

El servidor de frame recibe un Paquete de Firma en el cuerpo POST. El servidor de frame debe responder con un 302 Found y un encabezado Location que comience con `http://` o `https://`.

### `link`

```html
<meta name="fc:frame:button:1" content="Farcaster Docs" />
<meta name="fc:frame:button:1:action" content="link" />
<meta name="fc:frame:button:1:target" content="https://docs.farcaster.xyz" />
```

La acción `link` redirige al usuario a una URL externa. Puedes usar esta acción para redirigir a una URL sin manejar una solicitud POST al servidor de frame.

Los clientes no realizan una solicitud al servidor de frame para acciones `link`. En su lugar, redirigen al usuario a la URL `target`.

### `mint`

```html
<meta name="fc:frame:button:1" content="Mint" />
<meta name="fc:frame:button:1:action" content="mint" />
<meta
  name="fc:frame:button:1:target"
  content="eip155:8453:0xf5a3b6dee033ae5025e4332695931cadeb7f4d2b:1"
/>
```

La acción `mint` permite al usuario acuñar un NFT. Los clientes que soportan relé o inician transacciones onchain pueden mejorar el botón de acuñación reléando una transacción o interactuando con la billetera del usuario. Los clientes que no lo hacen recurren a enlazar a una URL externa.

La propiedad `target` debe ser una dirección `CAIP-10` válida, más un ID de token opcional.

### `tx`

```html
<meta property="fc:frame:button:1" content="Transaction" />
<meta property="fc:frame:button:1:action" content="tx" />
<meta
  property="fc:frame:button:1:target"
  content="https://frame.example.com/get_tx_data"
/>
<meta
  property="fc:frame:button:1:post_url"
  content="https://frame.example.com/tx_callback"
/>
```

La acción `tx` permite que un frame solicite al usuario realizar una acción en su billetera conectada. A diferencia de otros tipos de acción, las acciones `tx` tienen múltiples pasos.

Primero, el cliente realiza una solicitud POST a la URL `target` para obtener datos sobre la acción de la billetera. El servidor de frame recibe un Paquete de Firma en el cuerpo POST, incluyendo la dirección de la billetera conectada. El servidor de frame debe responder con un 200 OK y una respuesta JSON describiendo la acción de la billetera:

```json
{
  method: "eth_sendTransaction",
  chainId: "eip155:10",
  params: {
    abi: [...], // ABI JSON del selector de función y cualquier error
    to: "0x00000000fcCe7f938e7aE6D3c335bD6a1a7c593D",
    data: "0x783a112b0000000000000000000000000000000000000000000000000000000000000e250000000000000000000000000000000000000000000000000000000000000001",
    value: "984316556204476",
  },
}
```

El cliente usa estos datos para solicitar una acción en la billetera del usuario. Si el usuario completa la acción, el cliente realiza una solicitud POST a la `post_url` con un Paquete de Firma que incluye el hash de la transacción o firma en `transaction_id` y la dirección usada en `address`. El servidor de frame debe responder con un 200 OK y otro frame. El servidor de frame puede monitorear el hash de la transacción para determinar si la transacción tiene éxito, revierte o expira.

#### Tipo de Respuesta de Acción de Billetera

Una respuesta de acción de billetera debe ser una de las siguientes:

##### EthSendTransactionAction

- `chainId`: Un ID de cadena CAIP-2 para identificar la red de la transacción (ej. Ethereum mainnet)
- `method`: Debe ser `"eth_sendTransaction"`
- `attribution`: Opcional. Devuelve `false` para omitir el [sufijo de atribución de calldata](https://www.notion.so/Frame-Transactions-Public-9d9f9f4f527249519a41bd8d16165f73?pvs=21). Si este valor es `undefined` o `true`, los clientes agregarán el sufijo de atribución.
- `params`:
  - `to`: dirección de destino de la transacción
  - `abi`: ABI JSON que **debe** incluir el tipo de función codificado y **debería** incluir tipos de error potenciales. Puede estar vacío.
  - `value`: valor de ether a enviar con la transacción en wei. Opcional.
  - `data`: calldata de la transacción. Opcional.

```ts
type EthSendTransactionAction = {
  chainId: string;
  method: 'eth_sendTransaction';
  attribution?: boolean;
  params: {
    abi: Abi | [];
    to: string;
    value?: string;
    data?: string;
  };
};
```

##### EthSignTypedDataV4

Ver [EIP-712](https://eips.ethereum.org/EIPS/eip-712).

- `chainId`: Un ID de cadena CAIP-2 para identificar la red de la transacción (ej. Ethereum mainnet)
- `method`: Debe ser `"eth_signTypedData_v4"`
- `params`:
  - `domain`: el dominio tipado
  - `types`: las definiciones de tipo para los datos tipados
  - `primaryType`: el tipo primario para extraer de types y usar en value.
  - `message`: mensaje tipado

```ts
type EthSignTypedDataV4Action = {
  chainId: string;
  method: 'eth_signTypedData_v4';
  params: {
    domain: {
      name?: string;
      version?: string;
      chainId?: number;
      verifyingContract?: string;
    };
    types: Record<string, unknown>;
    primaryType: string;
    message: Record<string, unknown>;
  };
};
```

**Cadenas Soportadas**

| Red      | ID de Cadena       |
| -------- | ------------------ |
| Ethereum | `eip155:1`         |
| Arbitrum | `eip155:42161`     |
| Base     | `eip155:8453`      |
| Degen    | `eip155:666666666` |
| Gnosis   | `eip155:100`       |
| Optimism | `eip155:10`        |
| Zora     | `eip155:7777777`   |
| Polygon  | `eip155:137`       |

| Testnet          | ID de Cadena      |
| ---------------- | ----------------- |
| Sepolia          | `eip155:11155111` |
| Arbitrum Sepolia | `eip155:421614`   |
| Base Sepolia     | `eip155:84532`    |
| Optimism Sepolia | `eip155:11155420` |

### Imágenes

Hay algunas reglas para servir imágenes en etiquetas `fc:frame:image`:

- El tamaño de la imagen debe ser < 10 MB.
- El tipo de imagen debe ser jpg, png o gif.
- La fuente de la imagen debe ser un recurso externo con encabezados de contenido o una URI de datos.

Los clientes pueden redimensionar imágenes más grandes o recortar aquellas que no encajen en su relación de aspecto. Las imágenes SVG no están permitidas porque pueden contener scripts y los clientes deben hacer un trabajo adicional para sanitizarlas.

Los servidores de frame pueden usar encabezados de caché para refrescar imágenes y ofrecer experiencias de primer frame más dinámicas:

- Los servidores de frame pueden usar la directiva `max-age` en el encabezado HTTP `Cache-Control` para asegurar que las imágenes en el frame inicial se refresquen automáticamente. Un `max-age` más bajo asegura que las imágenes se actualicen regularmente sin interacciones del usuario.
- Los desarrolladores de aplicaciones deben respetar los encabezados de caché establecidos por la imagen original del frame, y sus implementaciones de proxy de imágenes no deben interferir con las duraciones.

## Mostrar frames en un feed

Las aplicaciones de Farcaster son responsables de renderizar frames a los usuarios y proxy sus interacciones de vuelta al servidor de frame en su nombre.

### Análisis de Frames

Cuando se encuentra una URL incrustada en un cast:

1. Las aplicaciones deben raspar los encabezados para verificar si la URL es un frame.
2. Si las etiquetas del frame son válidas, las aplicaciones deben renderizar el frame.
3. Si las etiquetas del frame son inválidas o ausentes, las aplicaciones deben recurrir a las etiquetas OpenGraph.
4. Si las etiquetas OG también están ausentes, las aplicaciones deben renderizar un mensaje de error de marcador de posición.

### Renderizado de Frames

Las aplicaciones pueden renderizar frames en cualquier momento en que muestren un cast a un espectador. Las siguientes reglas aplican al renderizado de frames:

1. Los botones deben mostrarse en orden ascendente de índice debajo de la imagen.
2. Los botones pueden mostrarse en múltiples filas si el espacio es una limitación.
3. Las entradas de texto deben mostrarse encima de los botones y debajo de la imagen.
4. Las etiquetas de entrada de texto deben mostrarse encima o dentro de la entrada de texto.
5. Las aplicaciones deben respetar la relación de aspecto establecida en la propiedad `fc:frame:image:aspect_ratio
