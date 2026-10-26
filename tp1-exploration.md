# TP1 : Exploration des API Docker et NetBox

## API Docker (`D=http://127.0.0.1:2375`)

Deux conteneurs lancés au départ, un seul étiqueté :

```bash
docker run -d --name web1 --label adminapi.managed=true nginx:alpine
docker run -d --name intrus nginx:alpine
```

Codes relevés avec `-s -o /dev/null -w "%{http_code}\n"` (ou `-i` sur les POST).

| Commande | Code |
|---|---|
| `curl -s $D/version` | 200 |
| `curl -s "$D/containers/json?all=true"` | 200 (2 conteneurs : web1 et intrus) |
| `curl -s --get $D/containers/json --data-urlencode 'all=true' --data-urlencode 'filters={"label":["adminapi.managed=true"]}'` | 200 (1 seul : web1 — intrus est invisible) |
| `curl -s $D/containers/web1/json` | 200 |
| `curl -X POST $D/containers/web1/stop` (1er appel) | 204 : conteneur arrêté |
| `curl -X POST $D/containers/web1/stop` (2e appel) | 304 : déjà arrêté, rien à faire |
| `curl $D/containers/nexistepas/json` | 404 : conteneur inconnu |
| `curl -X POST $D/containers/web1/start` | 204 |

## API NetBox (`N=http://127.0.0.1:8080/api`)

Variables utilisées : `A="Authorization: Bearer $NETBOX_TOKEN"` et `J="Content-Type: application/json"`.

| Commande | Code |
|---|---|
| `curl -X POST $N/users/tokens/provision/ -H "$J" -d '{"username":"admin","password":"admin"}'` | 201 : token créé (rangé dans `.env`, format `nbt_<key>.<token>`) |
| `curl $N/status/ -H "$A"` | 200 |
| `curl -X POST $N/virtualization/cluster-types/ -H "$A" -H "$J" -d '{"name":"Docker","slug":"docker"}'` | 201 : type id 1 |
| `curl -X POST $N/virtualization/clusters/ -H "$A" -H "$J" -d '{"name":"docker-local","type":1}'` | 201 : cluster id 1 |
