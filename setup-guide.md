
# Setup Guide

Para conexão externa do Core é necessário fazer com que os containers se comuniquem entre si.  
Isso só é possível se colocarmos todos os containers na mesma rede (`network`).  
Dessa maneira, é possível a comunicação e o acesso de comandos presentes nos mesmos.

A seguir, o passo a passo realizado:

---

## 1° Passo: Criação da rede

Criamos a rede que irá agrupar todos os containers necessários:

```bash
sudo docker network create <MY-BRIGDE-NETWORK> --subnet=<YOUR_IP>
```

---

## 2° Passo: Migração dos containers

Neste passo, migramos todos os containers da rede `oia-cn5g-public-net` para a rede criada `MY-BRIGDE-NETWORK`.

```bash
docker ps -a
```

Primeiro desconectamos os containers da rede antiga e depois conectamos na nova rede:

```bash
docker network disconnect oia-cn5g-public-net <TAG_CONTAINERS>
docker network connect my-brigde-network <TAG_CONTAINERS>
```

Repetimos esse processo para todos os containers.  
Para verificar se a rede realmente foi alterada:

```bash
docker inspect <TAG_CONTAINERS>
```

---

## 3° Passo: Alteração no `docker-compose.yaml`

Agora é preciso modificar o arquivo `docker-compose.yaml`, adicionando a nova rede :

```bash
cd oai-cn5g/
sudo nano docker-compose.yaml
```

Definição da rede no arquivo:

```yaml
networks: 
  public_net:
    driver: bridge
    name: MY-BRIGDE-NETWORK
    external: true
    ipam:
      config:
        - subnet: YOUR_IP
    driver_opts:
      com.docker.network.bridge.name: "MY-BRIGDE-NETWORK"
```

---

## 4° Passo: Subindo a rede e conexão com gNodeB e UE

```bash
cd oai-cn5g/
docker compose up
```

Executando o gNodeB:

```bash
cd UERANSIM
build/nr-gnb -c config/open5gs-gnb.yaml
```

Executando o UE:

```bash
cd UERANSIM
sudo build/nr-ue -c config/open5gs-ue.yaml
```

Testando a conectividade:

```bash
ping -I uesimtun0 google.com
```

---
