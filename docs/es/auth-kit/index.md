# AuthKit

[![Versión de NPM](https://img.shields.io/npm/v/@farcaster/auth-kit)](https://www.npmjs.com/package/@farcaster/auth-kit)

AuthKit es una biblioteca de React que permite a los usuarios iniciar sesión en tu aplicación con una cuenta de Farcaster.

<iframe src="https://farcaster-auth-kit-vite-demo.replit.app/" width="700" height="500" />

Haz clic en "Sign in With Farcaster" arriba para probarlo en la web o haz clic [aquí](https://sign-in-with-farcaster-demo.replit.app/) para móvil.

### ¿Cómo funciona?

Utiliza el estándar [Sign In With Farcaster](#sign-in-with-farcaster-siwf) internamente, que conceptualmente es similar a "Iniciar sesión con Google". Cuando se integra, AuthKit hará lo siguiente:

1. Mostrará un botón de "Sign in with Farcaster" al usuario.
2. Esperará a que el usuario haga clic, escanee un código QR y apruebe la solicitud en Warpcast.
3. Recibirá y verificará una firma de Warpcast.
4. Mostrará la foto de perfil y el nombre de usuario del usuario que ha iniciado sesión.
