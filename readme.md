# node-red-contrib-mikrotikros7

A Node-RED node for MikroTik RouterOS 6.43+ and RouterOS 7 devices.

**Author:** Aaron Lewis  
**Repository:** [github.com/a-lewis/node-red-contrib-mikrotikros7](https://github.com/a-lewis/node-red-contrib-mikrotikros7)

---

## Requirements

- Node-RED 2.0.0 or higher
- Node.js 14 or higher
- MikroTik device running RouterOS 6.43+ or RouterOS 7

---

## Install

Install via the palette manager in the Node-RED admin UI (no restart needed).

Alternatively run the following in your Node-RED user directory (typically `~/.node-red`):

```
npm install node-red-contrib-mikrotikros7
```

Then restart Node-RED.

---

## Config

Add a **mikrotik-device** config node with your device details:

| Field    | Description                           | Default      |
|----------|---------------------------------------|--------------|
| Host     | IP address or hostname of your router | 192.168.88.1 |
| Port     | API port                              | 8728         |
| Username | RouterOS username                     | admin        |
| Password | RouterOS password                     |              |
| SSL      | Enable TLS connection (port 8729)     | false        |

---

## Actions

| Action      | Description                                   |
|-------------|-----------------------------------------------|
| log         | Retrieve the system log                       |
| resources   | Retrieve CPU, memory and uptime               |
| wifi        | Retrieve the WiFi registration table          |
| connections | Retrieve active firewall connections          |
| reboot      | Reboot the device                             |
| raw         | Send any RouterOS API command via msg.payload |

---

## Message Properties

Every property can be overridden per message:

| Property     | Description                       |
|--------------|-----------------------------------|
| msg.host     | Override device host              |
| msg.port     | Override device port              |
| msg.username | Override username                 |
| msg.password | Override password                 |
| msg.ssl      | Override SSL setting (true/false) |
| msg.action   | Override action (1-5, 9)         |
| msg.payload  | Command for raw mode              |

---

## Examples

### Get system resources

Inject a timestamp, connect to a mikrotik node set to **resources**, wire to a debug node.

`msg.payload` output:
```json
{
  "platform": "MikroTik",
  "board-name": "RB4011iGS+",
  "version": "7.14.3",
  "uptime": "2w3d4h",
  "cpu-load": "3",
  "free-memory": "512180224",
  "total-memory": "1073741824"
}
```

---

### Get connected WiFi clients

Set action to **wifi**. `msg.payload` output:
```json
[
  {
    "interface": "wifi1",
    "mac-address": "AA:BB:CC:DD:EE:FF",
    "signal-strength": "-62",
    "tx-rate": "144Mbps",
    "rx-rate": "144Mbps",
    "uptime": "1h23m"
  }
]
```

---

### Raw command — print IP addresses

Set action to **raw** and inject:
```json
{ "payload": "/ip/address/print" }
```

`msg.payload` output:
```json
[
  {
    "address": "192.168.88.1/24",
    "interface": "bridge",
    "network": "192.168.88.0"
  }
]
```

---

### Raw command — add a firewall rule

Set action to **raw** and inject an array:
```json
{
  "payload": [
    "/ip/firewall/filter/add",
    "=chain=input",
    "=protocol=tcp",
    "=dst-port=22",
    "=action=drop"
  ]
}
```

Commands that return no data (add, remove, reboot) resolve with `[]`.

---

### Override connection per message

Target different routers dynamically without changing the config node:
```json
{
  "host": "10.0.0.1",
  "username": "admin",
  "password": "secret",
  "action": "9",
  "payload": "/system/identity/print"
}
```

---

## License

ISC
