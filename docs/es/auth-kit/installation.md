# Instalación

Instala auth-kit y su dependencia [viem](https://viem.sh/).

```sh
npm install @farcaster/auth-kit viem
```

**Nota:** auth-kit es una biblioteca de [React](https://react.dev/). Si estás usando un framework diferente, revisa la [biblioteca cliente](./client/introduction.md) en su lugar.

### 1. Importa las bibliotecas

Importa auth-kit y los estilos CSS.

```tsx
import '@farcaster/auth-kit/styles.css';
import { AuthKitProvider } from '@farcaster/auth-kit';
import { SignInButton } from '@farcaster/auth-kit';
```

### 2. Configura tu proveedor

Configura un proveedor con una URL RPC de Optimism, el dominio de tu aplicación y la URL de inicio de sesión, y envuelve tu aplicación en él.

```tsx
const config = {
  rpcUrl: 'https://mainnet.optimism.io',
  domain: 'example.com',
  siweUri: 'https://example.com/login',
};

const App = () => {
  return <AuthKitProvider config={config}>{/*   Tu App   */}</AuthKitProvider>;
};
```

### 3. Añade un botón de conexión

Renderiza el componente `SignInButton`. Cuando el usuario haga clic en este botón, se le pedirá que complete el inicio de sesión usando su aplicación de billetera Farcaster.

```tsx
export const Login = () => {
  return <SignInButton />;
};
```

### 4. Lee el perfil del usuario

Opcionalmente, obtén detalles sobre el usuario conectado en cualquier parte de tu aplicación con `useProfile`.

```tsx
import { useProfile } from '@farcaster/auth-kit';

export const UserProfile = () => {
  const {
    isAuthenticated,
    profile: { username, fid },
  } = useProfile();
  return (
    <div>
      {isAuthenticated ? (
        <p>
          Hola, {username}! Tu fid es: {fid}
        </p>
      ) : (
        <p>No has iniciado sesión.</p>
      )}
    </div>
  );
};
```
