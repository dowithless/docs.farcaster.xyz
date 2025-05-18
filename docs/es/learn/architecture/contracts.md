# Contratos

Una cuenta de Farcaster se gestiona y protege en la cadena utilizando los contratos de Farcaster. Esta sección proporciona una visión general de alto nivel y omite algunos detalles de implementación. Para obtener una imagen completa, consulta el [repositorio de contratos](https://github.com/farcasterxyz/contracts/).

<br>

Hay tres contratos principales desplegados en OP Mainnet:

- **Id Registry** - crea nuevas cuentas
- **Storage Registry** - alquila almacenamiento a las cuentas
- **Key Registry** - añade y elimina claves de aplicaciones de las cuentas

<br>

![Contratos de Registro](/assets/registry-contracts.png)

Los contratos están desplegados en las siguientes direcciones:

| Contrato        | Dirección                                                                                                                        |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| IdRegistry      | [0x00000000fc6c5f01fc30151999387bb99a9f489b](https://optimistic.etherscan.io/address/0x00000000fc6c5f01fc30151999387bb99a9f489b) |
| StorageRegistry | [0x00000000fcce7f938e7ae6d3c335bd6a1a7c593d](https://optimistic.etherscan.io/address/0x00000000fcce7f938e7ae6d3c335bd6a1a7c593d) |
| KeyRegistry     | [0x00000000fc1237824fb747abde0ff18990e59b7e](https://optimistic.etherscan.io/address/0x00000000fc1237824fb747abde0ff18990e59b7e) |

### Id Registry

El Id Registry permite a los usuarios registrar, transferir y recuperar cuentas de Farcaster. Una cuenta se identifica por un número único (el fid) que se asigna a una dirección de Ethereum en el registro. Una dirección de Ethereum solo puede poseer una cuenta a la vez, aunque puede transferirla libremente a otras cuentas. También puede especificar una dirección de recuperación que puede transferir la cuenta en cualquier momento.

### Storage Registry

El Storage Registry permite a las cuentas alquilar [almacenamiento](../what-is-farcaster/messages.md#storage) realizando un pago en ETH. Los precios del almacenamiento los establecen los administradores en USD y se convierten a ETH utilizando un oráculo de Chainlink. El precio aumenta o disminuye según la oferta y la demanda.

### Key Registry

El Key Registry permite a las cuentas emitir claves a aplicaciones, para que puedan publicar mensajes en su nombre. Las claves se pueden añadir o eliminar en cualquier momento. Para añadir una clave, una cuenta debe enviar la clave pública de un par de claves EdDSA junto con una firma del solicitante. El solicitante puede ser la propia cuenta o una aplicación que quiera operar en su nombre.
