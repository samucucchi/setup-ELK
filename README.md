```bash
# Elasticsearch
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.5.0-x86_64.rpm
sudo rpm --install elasticsearch-8.5.0-x86_64.rpm

# Kibana
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.5.0-x86_64.rpm
shasum -a 512 kibana-8.5.0-x86_64.rpm 
sudo rpm --install kibana-8.5.0-x86_64.rpm
```

# Setup Elasticsearch
Eseguire tutti i punti, prima con l'```initial_master_node```, 
1. Installare Elasticsearch 
2. ***MASTER***  Generare enrollment token, (dopo aver avviato Elastisearch) uno per nodi, uno per kibana: 
    ``` /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node ```

    ``` /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana ```
3. **NON - MASTER** da fare come secondo punto, poi continuare con i punti successivi
    ```
    bin\elasticsearch --enrollment-token <enrollment-token> (node, no kibana)
    ```

4. Modificare ``` /etc/elasticsearch/elasticsearch.yml ```, con: 
    ```
    cluster.name: academy
    node.name: academy-1 (in base al nome del'host)
    node.roles: [ data, master ] (l'host con Kibana, si imposta anche voting_only)
    bootstrap.memory_lock: true
    network.host: 141.94.168.201 (in base all'ip dell'host)
    discovery.seed_hosts: [ "141.94.168.201:9300", "141.94.169.226:9300", "141.94.169.86:9300" ]
    cluster.initial_master_nodes: ["academy-1"] (solo nel master)
    ```
5. Modificare ``` jvm.options ``` (impostare a metà RAM): 
    ```
    -Xms2g
    -Xmx2g
    ```
6. ``` systemctl edit elasticsearch ```, aggiungere: 
    ```
    [Service]
    LimitMEMLOCK=infinity
    ```

# Setup Kibana 
1. In ```/etc/kibana/kibana.yml```, 
    ```
    server.host: "141.94.169.86"
    elasticsearch.ssl.verificationMode: none
    ```
2. Connettersi all'indirzzo specificato in ```server.host``` ed inserire l'enrollment token generato prima dal master node. 
