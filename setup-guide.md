
# Core 5G Network Setup Guide

This guide explains how to enable external connectivity for the Core 5G network by configuring Docker containers to communicate on the same network.  
Following these steps ensures seamless interaction between containers and allows proper operation of gNodeB and UE.

---

## Step 1: Create a Docker Network

Create a network that will group all necessary containers:

```bash
sudo docker network create <MY-BRIDGE-NETWORK> --subnet=<YOUR_SUBNET>
```

---

## Step 2: Migrate Containers to the New Network

Move all containers from the old network `oia-cn5g-public-net` to the newly created `MY-BRIDGE-NETWORK`.

1. List all containers:

```bash
docker ps -a
```

2. Disconnect containers from the old network and connect them to the new one:

```bash
docker network disconnect oia-cn5g-public-net <CONTAINER_NAME>
docker network connect my-bridge-network <CONTAINER_NAME>
```

Repeat this process for all containers.  
To verify that a container is now on the correct network:

```bash
docker inspect <CONTAINER_NAME>
```

---

## Step 3: Update `docker-compose.yaml`

Modify the `docker-compose.yaml` file to use the new network:

```bash
cd oai-cn5g/
sudo nano docker-compose.yaml
```

Add the network definition:

```yaml
networks: 
  public_net:
    driver: bridge
    name: MY-BRIDGE-NETWORK
    external: true
    ipam:
      config:
        - subnet: YOUR_SUBNET
    driver_opts:
      com.docker.network.bridge.name: "MY-BRIDGE-NETWORK"
```

---

## Step 4: Start the Network and Connect gNodeB and UE

Bring up the containers:

```bash
cd oai-cn5g/
docker compose up
```

Run the gNodeB:

```bash
cd UERANSIM
build/nr-gnb -c config/open5gs-gnb.yaml
```

Run the UE:

```bash
cd UERANSIM
sudo build/nr-ue -c config/open5gs-ue.yaml
```

Test connectivity:

```bash
ping -I uesimtun0 google.com
```

---

## Notes & Tips

- Ensure all container names are correct when disconnecting and connecting to networks.  
- Make sure the subnet in `<YOUR_SUBNET>` does not conflict with your host machine network.  
- Use `docker inspect <CONTAINER_NAME>` to check assigned IPs and network configuration.  
- If connectivity fails, restart the containers after network migration.  
- For troubleshooting UE connectivity, ensure `uesimtun0` interface is up.
