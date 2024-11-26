# Apex prime public testnet relay and tooling

This docker compose file is starting following containers:

* prime-relay (standalone, prerequisite for dbsync and wallet-api)
* ogmios (requires prime-relay)
* postgres (standalone, prerequisite for dbsync and blockfrost)
* dbsync (requires prime-relay and postgres)
* blockfrost (requires postgres and consequently dbsync to keep it up to date)
* wallet-api (requires prime-relay)
* icarus (requires wallet-api)

The docker compose file is envisioned as example of available tooling and will start all of them in sequence.
Feel free to exclude/modify listed services as per your requirements, following the dependency comments.

For detals consult the docker compose file but at the time of writing, the followinge versions apply:

| Component   | Version      | Docker registry                      |
|-------------|--------------|--------------------------------------|
| prime-relay |        8.9.4 | ghcr.io/intersectmbo/cardano-node    |
| ogmios      |       v6.4.0 | cardanosolutions/ogmios              |
| postgres    | 14.10-alpine | postgres                             |
| dbsync      |     13.2.0.2 | ghcr.io/intersectmbo/cardano-db-sync |


## Prerequisites:

* intel based linux system (this was tested on)
* docker with compose
* network


## Start procedure

Run:

```
docker compose up -d
```


## Apex node relay

This is a relay node connected to a running `prime-mainnet-894` network. All `cardano-cli` commands apply as usual. For example:

To check the tip (at the moment it is about 10 min to sync, will definitely vary over time):

```
docker exec -it prime-mainnet-894-prime-relay-1 cardano-cli query tip --network-magic 764824073 --socket-path /ipc/node.socket
```


## Ogmios API

For ogmios api consult the [online documentation](https://ogmios.dev/api/v6.4/).
To check ogmios http api point a browser to `localhost` port `1337`, for example:

```
http://localhost:1337/
```


## DbSync

DbSync is indexer created as ETL tool coprised of three components:

* running node relay (to track blockchain state)
* dbsync etl tool (to react to block events, parse them and store them to postgres database)
* postgres database

Credentials to access the postgres database are in `secrets/dbsync/` folder.


## Remove procedure

To remove containers and volumes, images will be left for fast restart:

```
docker compose down
docker volume rm \
  prime-mainnet-894_db-sync-data \
  prime-mainnet-894_node-db \
  prime-mainnet-894_node-ipc \
  prime-mainnet-894_postgres
```
