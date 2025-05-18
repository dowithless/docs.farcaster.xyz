# Buscar cuenta por nombre de usuario

Si tienes el nombre de un usuario y deseas encontrar su cuenta, necesitarás utilizar uno de estos métodos dependiendo del tipo de nombre de usuario que tenga.

## Nombres ENS Offchain (Fnames)

Si el usuario tiene un nombre ENS offchain como `@alice`, deberás llamar al [Fname Registry](/es/reference/fname/api#get-current-fname-or-fid).

```bash
curl https://fnames.farcaster.xyz/transfers/current?name=farcaster | jq
{
  "transfers": [
    {
      "id": 1,
      "timestamp": 1628882891,
      "username": "farcaster",
      "owner": "0x8773442740c17c9d0f0b87022c722f9a136206ed",
      "from": 0,
      "to": 1,
      "user_signature": "0xa6fdd2a69deab5633636f32a30a54b21b27dff123e6481532746eadca18cd84048488a98ca4aaf90f4d29b7e181c4540b360ba0721b928e50ffcd495734ef8471b",
      "server_signature": "0xb7181760f14eda0028e0b647ff15f45235526ced3b4ae07fcce06141b73d32960d3253776e62f761363fb8137087192047763f4af838950a96f3885f3c2289c41b"
    }
  ]
}
```

Esto devuelve la transferencia más reciente asociada al nombre si está registrado. Ten en cuenta que la creación de un Fname es una transferencia desde la dirección cero a la dirección de custodia. El campo `to` indica el FID actual que posee el nombre.

## Nombres ENS Onchain

Si el usuario tiene un nombre ENS onchain como `@alice.eth`, la forma más fácil de hacerlo es con el [replicador](../apps/replicate.md) de Hubble. Este indexa datos onchain y offchain y te permite encontrar fácilmente lo que buscas.

Una vez configurado, consulta la tabla `fnames` en la base de datos del replicador para obtener el FID de la cuenta:

```sql
SELECT username, fid
FROM fnames
WHERE username = 'farcaster.eth'
order by updated_at desc
limit 1;
```

Consulta [aquí](/es/reference/replicator/schema#fnames) para más detalles sobre el esquema de la tabla del replicador.
