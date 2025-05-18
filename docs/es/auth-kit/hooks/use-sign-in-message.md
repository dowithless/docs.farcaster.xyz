# `useSignInMessage`

Hook para leer el mensaje de "Sign in With Farcaster" y la firma utilizada para autenticar al usuario.

Si estás proporcionando el mensaje y la firma a una API backend, quizás quieras usar este hook.

```tsx
import { useSignInMessage } from '@farcaster/auth-kit';

function App() {
  const { message, signature } = useSignInMessage();

  return (
    <div>
      <p>Firmaste: {message}</p>
      <p>Tu firma: {signature}</p>
    </div>
  );
}
```

## Retorna

```ts
{
  message: string;
  signature: Hex;
}
```

| Parámetro   | Descripción                                          |
| ----------- | ---------------------------------------------------- |
| `message`   | Mensaje SIWE firmado por el usuario.                 |
| `signature` | Firma producida por la wallet Farcaster del usuario. |
