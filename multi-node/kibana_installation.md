## To install kibana, follow these steps (in this test we installed kibana on the same EC2 of the first node):
### Step 1: Install Kibana
1. Since we already downloaded the public key we could install kibana directly by running the following command:
```bash
sudo apt-get update && sudo apt-get install kibana
```
2. (On the first node)Run the elasticsearch-create-enrollment-token command with the -s kibana option to generate a Kibana enrollment token:
```bash
sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
```
3. Copy the output of the command, which will look something like this:

xxxxxx...1pOcWRlWWd2OGcifQ==

4. in kibana host server, run the following two commands to enable Kibana to run as a service using systemd, enabling Kibana to start automatically when the host system reboots.
```bash
sudo systemctl daemon-reload
sudo systemctl enable kibana.service
```
5. In a terminal, run the ip add command and copy the value for the host inet IP address.

6. Open the Kibana configuration file in a text editor:
```bash
sudo vim /etc/kibana/kibana.yml
```
7. Uncomment the line #server.host: localhost and replace the default address with the inet value that you copied from the ifconfig command. For example:

server.host: 172.31.0.28

8. Start the Kibana service:
```bash
sudo systemctl start kibana.service
```
9. Check the status of the Kibana service:
```bash
sudo systemctl status kibana.service
```
10. you will see in the output of status command the follwing:
a URL is shown with:

- A host address to access Kibana
- A six digit verification code

For example:

Kibana has not been configured.
Go to http://10.128.0.28:5601/?code=XXXXXX to get started.

⚠️⚠️ if not, you can get the verification code using this command in the node that is running kibana (make sure to copy the code for a later use):
```bash
sudo /usr/share/kibana/bin/kibana-verification-code
```
11. Open a web browser and navigate the public ip of the instance running kibana (exp: http://3.88.x.x:5601). You will be prompted to enter Enrollment token and the verification code (you already copied those values in previous steps). you will be redirected to the Kibana login page.
12. Enter the username and password that you used to set up the Elasticsearch master node. You will be redirected to the Kibana home page.
13. Click Log in. Kibana is now fully set up and communicating with your Elasticsearch cluster!
