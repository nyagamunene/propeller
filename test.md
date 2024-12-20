# Test

Start docker composition

```bash
cd docker
docker compose up
```

Login as admin user

```bash
USER_TOKEN=$(magistrala-cli users token admin 12345678 | jq -r .access_token)
```

Create a domain

```bash
DOMAIN_ID=$(magistrala-cli domains create demo demo $USER_TOKEN | jq -r .id)
```

Create a thing called manager

```bash
magistrala-cli things create '{"name": "Propeller Manager", "tags": ["manager", "propeller"], "status": "enabled"}' $DOMAIN_ID $USER_TOKEN
```

Set the following environment variables from the respose

```bash
export MANAGER_THING_ID=""
export MANAGER_THING_KEY=""
```

Create a channel called manager

```bash
magistrala-cli channels create '{"name": "Propeller Manager", "tags": ["manager", "propeller"], "status": "enabled"}' $DOMAIN_ID $USER_TOKEN
```

Set the following environment variables from the respose

```bash
export MANAGER_CHANNEL_ID=""
```

Connect the thing to the manager channel

```bash
magistrala-cli things connect $MANAGER_THING_ID $MANAGER_CHANNEL_ID $DOMAIN_ID $USER_TOKEN
```

Create a thing called proplet

```bash
magistrala-cli things create '{"name": "Propeller Proplet", "tags": ["proplet", "propeller"], "status": "enabled"}' $DOMAIN_ID $USER_TOKEN
```

Set the following environment variables from the respose

```bash
export PROPLET_THING_ID=""
export PROPLET_THING_KEY=""
```

Connect the thing to the manager channel

```bash
magistrala-cli things connect $PROPLET_THING_ID $MANAGER_CHANNEL_ID $DOMAIN_ID $USER_TOKEN
```

Publish create message to the manager channel. This creates a new proplet.

```bash
mosquitto_pub -u $PROPLET_THING_ID -P $PROPLET_THING_KEY -I propeller -t channels/$MANAGER_CHANNEL_ID/messages/control/proplet/create -h localhost -m "{\"proplet_id\": \"$PROPLET_THING_ID\", \"name\": \"proplet-1\"}"
```

Publish alive message to the manager channel. This updates the proplet.

```bash
mosquitto_pub -u $PROPLET_THING_ID -P $PROPLET_THING_KEY -I propeller -t channels/$MANAGER_CHANNEL_ID/messages/control/proplet/alive -h localhost -m "{\"proplet_id\": \"$PROPLET_THING_ID\"}"
```

To start the manager, run the following command

```bash
export MANAGER_THING_ID=""
export MANAGER_THING_KEY=""
export PRMANAGER_CHANNEL_ID=""
export PROPLET_THING_ID=""
export PROPLET_THING_KEY=""
propeller-manager
```

To start the proplet, run the following command

```bash
export MANAGER_THING_ID=""
export MANAGER_THING_KEY=""
export PROPLET_CHANNEL_ID=""
export PROPLET_THING_ID=""
export PROPLET_THING_KEY=""
propeller-proplet
```
