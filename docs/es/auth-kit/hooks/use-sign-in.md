# `useSignIn`

Hook para iniciar sesión de un usuario. Se conecta al servidor de retransmisión, genera un enlace de inicio de sesión para presentar al usuario y sondea el servidor de retransmisión para obtener la firma de la billetera Farcaster del usuario.

Si deseas construir tu propio componente de inicio de sesión con una interfaz personalizada, utiliza el hook `useSignIn`.

```tsx
import { useSignIn, QRCode } from '@farcaster/auth-kit';

function App() {
  const {
    signIn,
    url,
    data: { username },
  } = useSignIn({
    onSuccess: ({ fid }) => console.log('Tu fid:', fid),
  });

  return (
    <div>
      <button onClick={signIn}>Iniciar sesión</button>
      {url && (
        <span>
          Escanea esto: <QRCode uri={url} />
        </span>
      )}
      {username && `¡Hola, ${username}!`}
    </div>
  );
}
```

## Parámetros

| Parámetro          | Tipo       | Descripción                                                                                              | Valor por defecto     |
| ------------------ | ---------- | -------------------------------------------------------------------------------------------------------- | --------------------- |
| `timeout`          | `number`   | Devuelve un error después de sondear durante este tiempo.                                                | `300_000` (5 minutos) |
| `interval`         | `number`   | Sondea el servidor de retransmisión para actualizaciones en este intervalo.                              | `1500` (1.5 segundos) |
| `nonce`            | `string`   | Un nonce aleatorio para incluir en el mensaje de Sign In With Farcaster.                                 | Ninguno               |
| `notBefore`        | `string`   | Hora en que el mensaje SIWF se vuelve válido. Cadena de fecha y hora ISO 8601.                           | Ninguno               |
| `expirationTime`   | `string`   | Hora en que el mensaje SIWF expira. Cadena de fecha y hora ISO 8601.                                     | Ninguno               |
| `requestId`        | `string`   | Un ID opcional específico del sistema para incluir en el mensaje SIWF.                                   | Ninguno               |
| `onSuccess`        | `function` | Callback invocado cuando el inicio de sesión se completa y el usuario está autenticado.                  | Ninguno               |
| `onStatusResponse` | `function` | Callback invocado cuando el componente recibe una actualización de estado del servidor de retransmisión. | Ninguno               |
| `onError`          | `function` | Función de callback para errores.                                                                        | Ninguno               |

## Retorna

```ts
  {
    signIn: () => void;
    signOut: () => void;
    connect: () => void;
    reconnect: () => void;
    isConnected: boolean;
    isSuccess: boolean;
    isPolling: boolean;
    isError: boolean;
    error: AuthClientError;
    channelToken: string;
    url: string;
    appClient: AppClient;
    data: {
        state: "pending" | "complete";
        nonce: string;
        message: string;
        signature: Hex;
        fid: number;
        username: string;
        bio: string;
        displayName: string;
        pfpUrl: string;
        custody?: Hex;
        verifications?: Hex[];
    },
    validSignature: boolean;
  };
```

| Parámetro            | Descripción                                                                                                                           |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `signIn`             | Llama a esta función después de `connect` para comenzar a sondear una firma.                                                          |
| `signOut`            | Llama a esta función para limpiar el estado de AuthKit y cerrar la sesión del usuario.                                                |
| `connect`            | Conecta al relé de autenticación y crea un canal.                                                                                     |
| `reconnect`          | Reconecta al relé e intenta nuevamente. Llama a esto en caso de un error.                                                             |
| `isConnected`        | Verdadero si AuthKit está conectado al servidor de retransmisión y tiene un canal activo.                                             |
| `isSuccess`          | Verdadero cuando el servidor de retransmisión devuelve una firma válida.                                                              |
| `isPolling`          | Verdadero cuando el estado del relé es `"pending"` y la aplicación está sondeando el servidor de retransmisión para una respuesta.    |
| `isError`            | Verdadero cuando ha ocurrido un error.                                                                                                |
| `error`              | Instancia de `AuthClientError`.                                                                                                       |
| `channelToken`       | Token del canal de conexión del relé.                                                                                                 |
| `url`                | URL de Sign in With Farcaster para presentar al usuario. Enlaza a Warpcast en v1.                                                     |
| `appClient`          | Instancia subyacente de `AppClient`.                                                                                                  |
| `data.state`         | Estado de la solicitud de inicio de sesión, ya sea `"pending"` o `"complete"`.                                                        |
| `data.nonce`         | Nonce aleatorio usado en el mensaje SIWE. Si no proporcionas un nonce personalizado como argumento al hook, deberías leer este valor. |
| `data.message`       | El mensaje SIWE generado.                                                                                                             |
| `data.signature`     | Firma hexadecimal producida por la billetera Warpcast del usuario.                                                                    |
| `data.fid`           | ID de Farcaster del usuario.                                                                                                          |
| `data.username`      | Nombre de usuario de Farcaster.                                                                                                       |
| `data.bio`           | Biografía de Farcaster del usuario.                                                                                                   |
| `data.displayName`   | Nombre para mostrar de Farcaster del usuario.                                                                                         |
| `data.pfpUrl`        | URL de la foto de perfil de Farcaster del usuario.                                                                                    |
| `data.custody`       | Dirección de custodia del FID del usuario.                                                                                            |
| `data.verifications` | Lista de direcciones verificadas del usuario.                                                                                         |
| `validSignature`     | Verdadero cuando la firma devuelta por el servidor de retransmisión es válida.                                                        |
