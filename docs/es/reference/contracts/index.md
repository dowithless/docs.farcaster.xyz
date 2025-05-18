# Resumen

Los contratos de Farcaster están desplegados en Optimism, una red de capa 2 de Ethereum. Existen tres contratos principales:  
Id Registry, Key Registry y Storage Registry. El acceso de escritura a los registros de ID y Key está controlado a través de los contratos Gateway.  
También hay un contrato auxiliar Bundler para facilitar el registro de un fid, añadir una clave y alquilar almacenamiento en una sola transacción.

![contracts.png](/assets/contracts.png)

## Id Registry

El contrato Id Registry se utiliza para llevar un registro de los IDs de Farcaster. Asigna un fid a una dirección de Ethereum propietaria.  
El propietario también puede designar una "dirección de recuperación" que puede usarse para recuperar el fid si pierde acceso a la dirección de registro.  
El registro inicial de un fid debe realizarse a través del [ID Gateway](#idgateway).

## Key Registry

El Key Registry asocia un id de Farcaster con cero o más claves públicas ed2559. Solo los mensajes firmados por una clave registrada aquí  
son considerados válidos por los hubs. Las claves registradas pueden ser revocadas por el propietario del fid, pero las claves revocadas  
no pueden volver a añadirse a ese fid. La misma clave puede registrarse en múltiples fids.  
Añadir una clave debe hacerse a través del [Key Gateway](#keygateway).

## Storage Registry

El Storage Registry permite que un fid alquile una o más "unidades" de almacenamiento en la red de Farcaster.  
El costo actual es de 7$ USD por unidad, por un año. Este pago debe realizarse en ETH.  
El registro de almacenamiento utiliza un oráculo de precios de ETH para determinar el costo actual en ETH y expone funciones para consultar este precio.  
Los pagos en exceso son reembolsados al emisor.

## IdGateway

El ID Gateway maneja la lógica adicional requerida para el registro inicial de un fid. Para prevenir spam,  
el gateway también requiere alquilar 1 unidad de almacenamiento.

## KeyGateway

De manera similar, el Key Gateway existe para el Key Registry. Añadir una clave a un fid debe hacerse a través del gateway.

## Bundler

El Bundler facilita el registro inicial permitiendo que un usuario registre un fid, añada una clave y alquile almacenamiento en una sola llamada de función.

## Código fuente

El repositorio del código fuente de los contratos puede encontrarse [aquí](https://github.com/farcasterxyz/contracts), incluyendo documentación más técnica [aquí](https://github.com/farcasterxyz/contracts/blob/main/docs/docs.md).
