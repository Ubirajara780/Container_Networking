
# Connecting OAI Core External IP

Para conexão externa do Core é necessário fazer com que os containers se comuniquem entre si.  
Isso só é possível se colocarmos todos os containers na mesma rede (`network`).  
Dessa maneira, é possível a comunicação e o acesso de comandos presentes nos mesmos.

A seguir, o passo a passo realizado:

---

## 1° Passo: Criação da rede

Criamos a rede que irá agrupar todos os containers necessários:

```bash
sudo docker network create my-brigde-network --subnet=150.165.37.0/24
```

---

## 2° Passo: Migração dos containers

Neste passo, migramos todos os containers da rede `oia-cn5g-public-net` para a rede criada `my-brigde-network`.

```bash
docker ps -a
```

Primeiro desconectamos os containers da rede antiga e depois conectamos na nova rede:

```bash
docker network disconnect oia-cn5g-public-net 7e6
docker network connect my-brigde-network 7e6
```

Repetimos esse processo para todos os containers.  
Para verificar se a rede realmente foi alterada:

```bash
docker inspect 7e6
```

---

## 3° Passo: Alteração no `docker-compose.yaml`

Agora é preciso modificar o arquivo `docker-compose.yaml`, adicionando a nova rede e os novos IPs:

```bash
cd oai-cn5g/
sudo nano docker-compose.yaml
```

Alterações realizadas:

```yaml
mysql:            ipv4_address: 150.165.37.211
ims:              ipv4_address: 150.165.37.210
oia-udr:          ipv4_address: 150.165.37.209
oia-udm:          ipv4_address: 150.165.37.207
oia-ausf:         ipv4_address: 150.165.37.208
oia-nrf:          ipv4_address: 150.165.37.206
oia-amf:          ipv4_address: 150.165.37.202
oia-smf:          ipv4_address: 150.165.37.203
oia-upf:          ipv4_address: 150.165.37.204
oai-ext-dn: 
  entrypoint: "ip route add 10.0.0.0/16 via 150.165.37.204 dev eth0; ip route; sleep infinity"
  ipv4_address: 150.165.37.205
```

Definição da rede no mesmo arquivo:

```yaml
networks: 
  public_net:
    driver: bridge
    name: my-bridge-network
    external: true
    ipam:
      config:
        - subnet: 150.165.37.0/24
    driver_opts:
      com.docker.network.bridge.name: "my-bridge-network"
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
