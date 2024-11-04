## The official doc for installing ES with Deb package: https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html
## The official doc for installling a self managed Elastic cluster: https://www.elastic.co/guide/en/elastic-stack/8.15/installing-stack-demo-self.html#install-stack-self-elasticsearch-first

## Step 1: Set up the first Elasticsearch node
### important note: you need to paste the output of the installation command into a text file to use it later, specifically the password of the cluster.

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg

sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt-get update && sudo apt-get install elasticsearch
```
1. Run the following two commands to enable Elasticsearch to run as a service using systemd. This enables Elasticsearch to start automatically when the host system reboots.
```bash
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
```
## Step 2: Configure the first Elasticsearch node for connectivity:
1. In a terminal, run the command ip add in ubuntu or debian and copy the value for the host inet IP address (for example, 10.128.0.84). You’ll need this value later.
```bash
ip add
```
2. Open the Elasticsearch configuration file in a text editor, such as vim:
```bash
sudo vi /etc/elasticsearch/elasticsearch.yml
```
3. In a multi-node Elasticsearch cluster, all Elasticsearch instances need to have the same name.
In the configuration file, uncomment the line #cluster.name: my-application and give the Elasticsearch instance any name that you’d like:
```bash
cluster.name: elastic-test-cluster
```
4. By default, Elasticsearch runs on localhost. In order for Elasticsearch instances on other nodes to be able to join the cluster, you’ll need to set up Elasticsearch to run on a routable IP address.

Uncomment the line #network.host: 192.168.0.1 and replace the default address with the value that you copied from the ifconfig command output (or ip add). For example:
```bash
network.host: (the ip address of the server 'private ip shown using the ip add command' example: 172.31.1.17) 
```
5. Elasticsearch needs to be enabled to listen for connections from other, external hosts.
```bash
transport.host: 0.0.0.0
```
6. Save and exit the configuration file.
## Step 3: Start Elasticsearch
1. Start the Elasticsearch service:
```bash
sudo systemctl start elasticsearch.service
```
2. Check the status of the Elasticsearch service:
```bash
sudo systemctl status elasticsearch.service
```
3. If the service is running, you should see output similar to the following (the password ):
```bash
sudo curl --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9200
```
the output should look like this:
```bash
{
  "name" : "ip-172-31-31-80",
  "cluster_name" : "elastic-test-cluster",
  "cluster_uuid" : "uMGp0MC1S4S1rSMW0f0_RQ",
  "version" : {
    "number" : "8.15.3",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "f97532e680b555c3a05e73a74c28afb666923018",
    "build_date" : "2024-10-09T22:08:00.328917561Z",
    "build_snapshot" : false,
    "lucene_version" : "9.11.1",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
``` 
4. now you should install elasticsearch in the other nodes and configure it to join the cluster. by following this  [documentation](./additional_nodes.md)


