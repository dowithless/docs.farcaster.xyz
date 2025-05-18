# API de Mensajes

La API de Mensajes te permite validar y enviar mensajes firmados del protocolo Farcaster al Hub. Ten en cuenta que el mensaje debe enviarse como el flujo de bytes codificado del protobuf (`Message.encode(msg).finish()` en typescript), como datos POST al endpoint.

La codificación de los datos POST debe establecerse como `application/octet-stream`. El endpoint devuelve el objeto Message como JSON si se envió o validó correctamente.

## submitMessage

Envía un mensaje firmado y serializado en protobuf al Hub.

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| | Este endpoint no acepta parámetros | |

**Ejemplo**

```bash
curl -X POST "http://127.0.0.1:2281/v1/submitMessage" \
     -H "Content-Type: application/octet-stream" \
     --data-binary "@message.encoded.protobuf"

```

**Respuesta**

```json
{
  "data": {
    "type": "MESSAGE_TYPE_CAST_ADD",
    "fid": 2,
    "timestamp": 48994466,
    "network": "FARCASTER_NETWORK_MAINNET",
    "castAddBody": {
      "embedsDeprecated": [],
      "mentions": [],
      "parentCastId": {
        "fid": 226,
        "hash": "0xa48dd46161d8e57725f5e26e34ec19c13ff7f3b9"
      },
      "text": "Texto del Cast",
      "mentionsPositions": [],
      "embeds": []
    }
  },
  "hash": "0xd2b1ddc6c88e865a33cb1a565e0058d757042974",
  "hashScheme": "HASH_SCHEME_BLAKE3",
  "signature": "3msLXzxB4eEYe...dHrY1vkxcPAA==",
  "signatureScheme": "SIGNATURE_SCHEME_ED25519",
  "signer": "0x78ff9a...58c"
}
```

### Autenticación

Si la autenticación RPC está habilitada en el servidor (usando `--rpc-auth usuario:contraseña`), necesitarás también proporcionar el nombre de usuario y contraseña al llamar a `submitMessage` usando Autenticación Básica HTTP.

**Ejemplo**

```bash
curl -X POST "http://127.0.0.1:2281/v1/submitMessage" \
     -u "usuario:contraseña" \
     -H "Content-Type: application/octet-stream" \
     --data-binary "@message.encoded.protobuf"
```

**Ejemplo en JS**

```Javascript
import axios from "axios";

const url = `http://127.0.0.1:2281/v1/submitMessage`;

const postConfig = {
  headers: { "Content-Type": "application/octet-stream" },
  auth: { username: "usuario", password: "contraseña" },
};

// Codifica el mensaje en un Buffer (de bytes)
const messageBytes = Buffer.from(Message.encode(castAdd).finish());

try {
  const response = await axios.post(url, messageBytes, postConfig);
} catch (e) {
  // manejar errores...
}
```

## validateMessage

Valida un mensaje firmado y serializado en protobuf con el Hub. Esto puede usarse para verificar que el hub considerará válido el mensaje. O para validar mensajes que no pueden enviarse (ej. acciones de Frame)

::: details
El hub valida lo siguiente para todos los mensajes:

- El fid está registrado
- El firmante está activo y registrado al fid
- El hash del mensaje es correcto
- La firma es válida y corresponde al firmante
- Cualquier otra validación específica del mensaje

Para mensajes FrameAction, ten en cuenta que el hub no valida que el castId sea realmente un cast existente. Tampoco valida que la URL del frame coincida con la URL incrustada en el cast. Asegúrate de comprobarlo si es importante para tu aplicación.

:::

**Parámetros de Consulta**
| Parámetro | Descripción | Ejemplo |
| --------- | ----------- | ------- |
| | Este endpoint no acepta parámetros | |

**Ejemplo**

```bash
curl -X POST "http://127.0.0.1:2281/v1/validateMessage" \
     -H "Content-Type: application/octet-stream" \
     --data-binary "@message.encoded.protobuf"

```

**Respuesta**

```json
{
  "valid": true,
  "message": {
    "data": {
      "type": "MESSAGE_TYPE_FRAME_ACTION",
      "fid": 2,
      "timestamp": 48994466,
      "network": "FARCASTER_NETWORK_MAINNET",
      "frameActionBody": {
        "url": "https://fcpolls.com/polls/1",
        "buttonIndex": 2,
        "inputText": "",
        "castId": {
          "fid": 226,
          "hash": "0xa48dd46161d8e57725f5e26e34ec19c13ff7f3b9"
        }
      }
    },
    "hash": "0xd2b1ddc6c88e865a33cb1a565e0058d757042974",
    "hashScheme": "HASH_SCHEME_BLAKE3",
    "signature": "3msLXzxB4eEYe...dHrY1vkxcPAA==",
    "signatureScheme": "SIGNATURE_SCHEME_ED25519",
    "signer": "0x78ff9a...58c"
  }
}
```

## Uso con Rust, Go u otros lenguajes de programación

Los mensajes deben firmarse con una clave de cuenta Ed25519 perteneciente al FID. Si estás usando un lenguaje de programación diferente a Typescript, puedes construir manualmente el objeto `MessageData` y serializarlo en el campo `data_bytes` del mensaje. Luego, usa `data_bytes` para calcular el `hash` y la `firma`. Consulta el ejemplo [`rust-submitmessage`](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/hub-web/examples) para más detalles.

```rust
use ed25519_dalek::{SecretKey, Signer, SigningKey};
use hex::FromHex;
use reqwest::Client;

use message::{CastAddBody, FarcasterNetwork, MessageData};
use protobuf::Message;


#[tokio::main]
async fn main() {
    let fid = 6833; // FID del usuario que envía el mensaje
    let network = FarcasterNetwork::FARCASTER_NETWORK_MAINNET;

    // Construye el mensaje cast add
    let mut cast_add = CastAddBody::new();
    cast_add.set_text("¡Bienvenido a Rust!".to_string());

    // Construye el objeto de datos del mensaje cast add
    let mut msg_data = MessageData::new();
    msg_data.set_field_type(message::MessageType::MESSAGE_TYPE_CAST_ADD);
    msg_data.set_fid(fid);
    msg_data.set_timestamp(
        (std::time::SystemTime::now()
            .duration_since(FARCASTER_EPOCH)
            .unwrap()
            .as_secs()) as u32,
    );
    msg_data.set_network(network);
    msg_data.set_cast_add_body(cast_add);

    let msg_data_bytes = msg_data.write_to_bytes().unwrap();

    // Calcula el hash blake3, truncado a 20 bytes
    let hash = blake3::hash(&msg_data_bytes).as_bytes()[0..20].to_vec();

    // Construye el mensaje real
    let mut msg = message::Message::new();
    msg.set_hash_scheme(message::HashScheme::HASH_SCHEME_BLAKE3);
    msg.set_hash(hash);

    // Firma el mensaje. Necesitas usar una clave de firma que corresponda al FID que estás añadiendo.
    // REEMPLAZA LA CLAVE PRIVADA CON LA TUYA
    let private_key = SigningKey::from_bytes(
        &SecretKey::from_hex("0x...").expect("Por favor proporciona una clave privada válida"),
    );
    let signature = private_key.sign(&msg_data_bytes).to_bytes();

    msg.set_signature_scheme(message::SignatureScheme::SIGNATURE_SCHEME_ED25519);
    msg.set_signature(signature.to_vec());
    msg.set_signer(private_key.verifying_key().to_bytes().to_vec());

    // Serializa el mensaje
    msg.set_data_bytes(msg_data_bytes.to_vec());
    let msg_bytes = msg.write_to_bytes().unwrap();

    // Finalmente, envía el mensaje a la red

    // Crea un cliente reqwest
    let client = Client::new();

    // Define la URL del endpoint
    let url = "http://127.0.0.1:2281/v1/submitMessage";

    // Realiza la petición POST
    let res = client
        .post(url)
        .header("Content-Type", "application/octet-stream")
        .body(msg_bytes)
        .send()
        .await
        .unwrap();

    // Comprueba si fue exitoso
    if res.status().is_success() {
        println!("Mensaje enviado con éxito.");
    } else {
        println!("Error al enviar el mensaje. Estado HTTP: {}", res.status());
    }
}
```
