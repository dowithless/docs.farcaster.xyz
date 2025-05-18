# API de Comprobaciones de Nombre de Usuario

## API

| Nombre del Método      | Tipo de Solicitud                             | Tipo de Respuesta                                 | Descripción                       |
| ---------------------- | --------------------------------------------- | ------------------------------------------------- | --------------------------------- |
| GetUsernameProof       | [UsernameProofRequest](#UsernameProofRequest) | [UserNameProof](#UserNameProof)                   | Comprobación de Nombre de Usuario |
| GetUserNameProofsByFid | [FidRequest](#FidRequest)                     | [UsernameProofsResponse](#UsernameProofsResponse) |                                   |

## UsernameProofRequest

| Campo | Tipo            | Etiqueta | Descripción |
| ----- | --------------- | -------- | ----------- |
| name  | [bytes](#bytes) |          |             |

## UsernameProofsResponse

| Campo  | Tipo                            | Etiqueta | Descripción |
| ------ | ------------------------------- | -------- | ----------- |
| proofs | [UserNameProof](#UserNameProof) | repetido |             |

## UserNameProof

| Campo     | Tipo                          | Etiqueta | Descripción |
| --------- | ----------------------------- | -------- | ----------- |
| timestamp | [uint64](#uint64)             |          |             |
| name      | [bytes](#bytes)               |          |             |
| owner     | [bytes](#bytes)               |          |             |
| signature | [bytes](#bytes)               |          |             |
| fid       | [uint64](#uint64)             |          |             |
| type      | [UserNameType](#UserNameType) |          |             |

## UserNameProof

| Campo     | Tipo                          | Etiqueta | Descripción |
| --------- | ----------------------------- | -------- | ----------- |
| timestamp | [uint64](#uint64)             |          |             |
| name      | [bytes](#bytes)               |          |             |
| owner     | [bytes](#bytes)               |          |             |
| signature | [bytes](#bytes)               |          |             |
| fid       | [uint64](#uint64)             |          |             |
| type      | [UserNameType](#UserNameType) |          |             |
