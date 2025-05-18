# `SignInButton`

El componente principal. Renderiza un botón "Iniciar sesión con Farcaster" que solicita al usuario escanear un código QR con su teléfono en un navegador web o redirige a un dispositivo móvil. Puedes usar la propiedad `onSuccess` como callback o el hook `useProfile` para acceder al estado de autenticación del usuario y su información de perfil.

**Nota:** Asegúrate de haber envuelto tu aplicación en un [`AuthKitProvider`](./auth-kit-provider.md) para usar el componente `SignInButton`.

```tsx
import { SignInButton } from '@farcaster/auth-kit';

export const Login = () => {
  return (
    <SignInButton
      onSuccess={({ fid, username }) =>
        console.log(`¡Hola, ${username}! Tu fid es ${fid}.`)
      }
    />
  );
};
```

## Props

| Prop               | Tipo       | Descripción                                                                                                   | Valor por defecto     |
| ------------------ | ---------- | ------------------------------------------------------------------------------------------------------------- | --------------------- |
| `timeout`          | `number`   | Devuelve un error después de este tiempo de espera.                                                           | `300_000` (5 minutos) |
| `interval`         | `number`   | Intervalo para consultar actualizaciones en el servidor de retransmisión.                                     | `1500` (1.5 segundos) |
| `nonce`            | `string`   | Un nonce aleatorio para incluir en el mensaje de Iniciar sesión con Farcaster.                                | Ninguno               |
| `notBefore`        | `string`   | Momento en que el mensaje se vuelve válido. Cadena de fecha y hora ISO 8601.                                  | Ninguno               |
| `expirationTime`   | `string`   | Momento en que el mensaje expira. Cadena de fecha y hora ISO 8601.                                            | Ninguno               |
| `requestId`        | `string`   | Un ID opcional específico del sistema para incluir en el mensaje.                                             | Ninguno               |
| `onSuccess`        | `function` | Callback que se invoca cuando el inicio de sesión se completa y el usuario está autenticado.                  | Ninguno               |
| `onStatusResponse` | `function` | Callback que se invoca cuando el componente recibe una actualización de estado del servidor de retransmisión. | Ninguno               |
| `onError`          | `function` | Función de callback para errores.                                                                             | Ninguno               |
| `onSignOut`        | `function` | Callback que se invoca cuando el usuario cierra sesión.                                                       | Ninguno               |
| `hideSignOut`      | `function` | Oculta el botón de Cerrar sesión.                                                                             | `false`               |
| `debug`            | `boolean`  | Muestra un panel de depuración con el estado interno de auth-kit.                                             | `false`               |

## Ejemplos

### Nonce personalizado

```tsx
import { SignInButton } from '@farcaster/auth-kit';

export const Login = ({ nonce }: { nonce: string }) => {
  return (
    <SignInButton
      nonce={nonce}
      onSuccess={({ fid, username }) =>
        console.log(`¡Hola, ${username}! Tu fid es ${fid}.`)
      }
    />
  );
};
```
