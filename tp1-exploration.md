# TP1 : Exploration des API Docker et NetBox

## API Docker (`D=http://127.0.0.1:2375`)

| Commande | Code |
|---|---|
| `curl -s $D/version` | 200 |
| `curl -s "$D/containers/json?all=true"` | 200 |
| `curl -s --get $D/containers/json --data-urlencode 'all=true' --data-urlencode 'filters={"label":["adminapi.managed=true"]}'` | 200 (seul web1 apparaît) |
| `curl -s $D/containers/web1/json` | 200 |
| `curl -X POST $D/containers/web1/stop` (1er appel) | 204 : conteneur arrêté |
| `curl -X POST $D/containers/web1/stop` (2e appel) | 304 : déjà arrêté, rien à faire |
| `curl $D/containers/nexistepas/json` | 404 : conteneur inconnu |
| `curl -X POST $D/containers/web1/start` | 204 |

## API NetBox (`N=http://127.0.0.1:8080/api`)

| Commande | Code |
|---|---|
| `curl -X POST $N/users/tokens/provision/ -d '{"username":"admin","password":"admin"}'` | 201 : token créé (rangé dans `.env`, format `nbt_<key>.<token>`) |
| `curl $N/status/ -H "Authorization: Bearer $NETBOX_TOKEN"` | 200 |
| `curl -X POST $N/virtualization/cluster-types/ -d '{"name":"Docker","slug":"docker"}'` | 201 : type id 1 |
| `curl -X POST $N/virtualization/clusters/ -d '{"name":"docker-local","type":1}'` | 201 : cluster id 1 |
