# `useProfile`

Hook para leer información sobre el usuario autenticado.

Puedes usar este hook para leer la información del perfil del usuario autenticado desde otros componentes dentro de tu app.

```tsx
import { useProfile } from '@farcaster/auth-kit';

function App() {
  const {
    isAuthenticated,
    profile: { username, fid, bio, displayName, pfpUrl },
  } = useProfile();

  return (
    <div>
      {isAuthenticated ? (
        <p>
          ¡Hola, {username}! Tu fid es: {fid}
        </p>
      ) : (
        <p>No has iniciado sesión.</p>
      )}
    </div>
  );
}
```

## Retorna

```ts
  {
    isAuthenticated: boolean;
    profile?: {
        fid?: number;
        username?: string;
        bio?: string;
        displayName?: string;
        pfpUrl?: string;
        custody?: Hex;
        verifications?: Hex[];
    },
  };
```

| Parámetro               | Descripción                                     |
| ----------------------- | ----------------------------------------------- |
| `isAuthenticated`       | Verdadero cuando el usuario ha iniciado sesión. |
| `profile.fid`           | ID de Farcaster del usuario.                    |
| `profile.username`      | Nombre de usuario.                              |
| `profile.bio`           | Texto de la biografía del usuario.              |
| `profile.displayName`   | Nombre para mostrar del usuario.                |
| `profile.pfpUrl`        | URL de la foto de perfil del usuario.           |
| `profile.custody`       | Dirección de custodia del FID del usuario.      |
| `profile.verifications` | Lista de direcciones verificadas del usuario.   |
