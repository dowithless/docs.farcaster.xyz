# `AuthKitProvider`

Envuelve tu aplicación en un `AuthKitProvider` para utilizar Farcaster Auth. Este componente proveedor almacena información de configuración sobre tu aplicación y la pone disponible para los componentes y hooks de auth-kit.

**Nota:** Debes crear un `AuthKitProvider` para usar Farcaster Connect. No olvides crearlo en el nivel superior de tu aplicación.

```tsx
const config = {
  domain: 'example.com',
  siweUri: 'https://example.com/login',
  rpcUrl: process.env.OP_MAINNET_RPC_URL,
  relay: 'https://relay.farcaster.xyz',
};

const App = () => {
  return <AuthKitProvider config={config}>{/*   Tu App   */}</AuthKitProvider>;
};
```

# Props

| Prop     | Tipo            | Requerido | Descripción                                                  |
| -------- | --------------- | --------- | ------------------------------------------------------------ |
| `config` | `AuthKitConfig` | No        | Objeto de configuración. Ver las opciones en la tabla abajo. |

Opciones del objeto `config`:

| Parámetro | Tipo     | Requerido | Descripción                                  | Valor por defecto             |
| --------- | -------- | --------- | -------------------------------------------- | ----------------------------- |
| `domain`  | `string` | No        | El dominio de tu aplicación.                 | `window.location.host`        |
| `siweUri` | `string` | No        | La URL de inicio de sesión de tu aplicación. | `window.location.href`        |
| `relay`   | `string` | No        | URL del servidor relay de Farcaster Auth     | `https://relay.farcaster.xyz` |
| `rpcUrl`  | `string` | No        | URL del servidor RPC de Optimism             | `https://mainnet.optimism.io` |
| `version` | `string` | No        | Versión de Farcaster Auth                    | `v1`                          |
