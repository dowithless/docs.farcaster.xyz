# Arquitectura

Un Hub es un daemon de un solo proceso que recibe datos de clientes, otros hubs y contratos de farcaster. Tiene cuatro componentes principales:

- **Motor de Almacenamiento**: verifica la validez de los mensajes, los persiste en disco y emite eventos.
- **Motor P2P**: establece una red gossipsub para intercambiar mensajes con otros hubs.
- **Motor de Sincronización**: maneja casos extremos cuando el gossip no logra entregar mensajes.
- **Servidores RPC**: proporciona APIs para leer y escribir datos en el hub.

### Motor de Almacenamiento

Los mensajes recibidos por Hubble se envían al motor de almacenamiento, que los reenvía al conjunto CRDT correspondiente. Una vez validados por el conjunto CRDT, los mensajes se persisten en [RocksDB](https://github.com/facebook/rocksdb) y se emiten eventos a los listeners.

Los conjuntos CRDT se implementan para cumplir con la especificación del protocolo Farcaster. El motor también rastrea el estado de los contratos de Farcaster, necesarios para validar el conjunto CRDT de Signer.

### Motor P2P

Hubble se conecta a otros peers a través de una red GossipSub establecida usando [LibP2P](https://github.com/libp2p/libp2p). Los mensajes integrados en el motor de almacenamiento se difunden inmediatamente a todos sus peers.

Hubble solo se conectará con peers de confianza y emplea una topología de red simple durante la beta. Solo se conecta con instancias conocidas que deben configurarse al inicio. En versiones posteriores, la topología de red se modificará para operar más cerca de una malla sin confianza.

### Motor de Sincronización

Hubble periódicamente realiza una [sincronización diff](https://github.com/farcasterxyz/protocol#41-synchronization) con otros peers para descubrir mensajes que puedan haberse perdido durante el gossip. Esto se realiza utilizando las APIs gRPC expuestas por cada instancia de Hub.

### Servidores RPC

Hubble expone una API gRPC y HTTP para leer y escribir datos en el hub. La forma principal de escribir datos en la red farcaster es llamar a `submitMessage` en un hub. El hub validará el mensaje y, si cumple con el protocolo, lo almacenará en el conjunto CRDT correspondiente y lo difundirá a otros peers. El hub también expone otras APIs para leer el estado de sus conjuntos.
