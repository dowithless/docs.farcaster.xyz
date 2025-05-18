# Hubble

[Hubble](https://github.com/farcasterxyz/hub-monorepo) es una implementación del [Protocolo Farcaster Hub](https://github.com/farcasterxyz/protocol), desarrollada en [TypeScript](https://www.typescriptlang.org/) y [Rust](https://www.rust-lang.org/).

Hubble crea una instancia privada de Farcaster en tu máquina. Se conecta con otras instancias y descarga una copia de toda la red. Los mensajes cargados en tu instancia de Hubble se transmitirán a la red.

Recomendamos ejecutar Hubble si estás desarrollando una aplicación, necesitas acceso a los datos más recientes o deseas ayudar a descentralizar la red.

## Instancias Alojadas

Las instancias de Hubble también pueden ser alojadas por otros proveedores de servicios.

- [Hubs x Neynar](https://hubs.neynar.com/)
- [Hubs x Pinata](https://pinata.cloud/pinata-hub)

## Instancias Públicas

El equipo de Farcaster opera una instancia de Hubble para uso público. No se garantiza su estabilidad y actualmente es de solo lectura.

```bash
url: hoyt.farcaster.xyz
httpapi_port: 2281
gossipsub_port: 2282
grpc_port: 2283
```
