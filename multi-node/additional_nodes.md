## to install elastic search and configure it after installing the first node follow these steps:
### Step 1: install elasticsearch (preferably you should save the output of the install command since it contains the password but you will not need because this node gonna join the master node)
1. download the public key and install elasticsearch:
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg

sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt-get update && sudo apt-get install elasticsearch
```
### ⚠️⚠️ Note: Don’t start the Elasticsearch service yet! There are a few more configuration steps to do before restarting.
2. To enable this second Elasticsearch node to connect to the first (master), you need to configure an enrollment token.

* ⚠️⚠️ Return to your terminal shell on the first Elasticsearch node and generate a node enrollment token(An enrollment token has a lifespan of 30 minutes. In case the elasticsearch-reconfigure-node command returns an Invalid enrollment token error, try generating a new token.):
```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node
```
3. Copy the output of the command, which will look something like this:

xxxxxx...1pOcWRlWWd2OGcifQ==

4. In the terminal shell of your second Elasticsearch node, pass the enrollment token as a parameter to the elasticsearch-reconfigure-node tool:
```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-reconfigure-node --enrollment-token <enrollment-token>
```
5. ⚠️⚠️ Answer the Do you want to continue prompt with yes (y). The new Elasticsearch node will be reconfigured.
6. In a terminal, run "ip add" and copy the value for the host inet IP address. You’ll need this value later.
7. Open the second Elasticsearch instance configuration file in a text editor:
```bash
sudo vim /etc/elasticsearch/elasticsearch.yml
```
8. In the configuration file, uncomment the line #cluster.name: my-application and set it to match the name you specified for the first Elasticsearch node (master node):
```bash
cluster.name: elastic-test-cluster
```
9. As with the first Elasticsearch node, you’ll need to set up Elasticsearch to run on a routable, external IP address. Uncomment the line #network.host: 92.168.0.1 and replace the default address with the value that you copied. For example:
```
network.host: (the ip address of the server 'private ip shown using the ip add command' example: 172.31.31.xx)
```
10. Save your changes and close the editor.
11. Start the Elasticsearch service:
```bash
sudo systemctl start elasticsearch.service
```
12. Check the status of the Elasticsearch service:
```bash
sudo systemctl status elasticsearch.service
```
13. Optionally, to view the progress as the second Elasticsearch node starts up and connects to the first Elasticsearch node, open a new terminal into the second node and tail the Elasticsearch log file:
```bash
sudo tail -f /var/log/elasticsearch/elasticsearch-demo.log
```
15. As a final check, run the following curl request on the new node to confirm that Elasticsearch is still running properly and viewable at the new node’s localhost IP address. Note that you need to replace $ELASTIC_PASSWORD with the same elastic superuser password that you used on the first Elasticsearch node.
```bash
sudo curl --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic:$ELASTIC_PASSWORD https://localhost:9200
```
### Set up additional Elasticsearch nodes
To set up your next Elasticsearch node, follow exactly the same steps as you did previously in this documentation. The process is identical for each additional Elasticsearch node that you would like to add to the cluster. As a recommended best practice, create a new enrollment token for each new node that you add.
### ⚠️⚠️ Note: after joining the cluster nodes, you can install kibana and configure it to connect to the cluster. by following this [documentation](./kibana_installation.md)
