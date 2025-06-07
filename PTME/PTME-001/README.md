# SIEM + AtomicRedTeam Attacks Integration Test 

*This experiment was peformed by me on 06.06.2025*

<p>Challenges of the experiment:</p>
<ul>
  <li>Configure SIEM (in this case it was ELK Stack, open source SIEM)</li>
  <li>Install and configure winlogbeat</li>
  <li>Set up indexes for winlogbeat in Kibana</li> 
  <li>Use AtomicRedTeam to test if SIEM collects the correct data and find malicious events</li>
  <li>Detect events</li>
</ul>

<p>Virtual Machines:</p>
- Ubuntu 22.04.05 LTS VM 
- Microsoft Windows 11 x64 VM

### 1. ELK (Elasticsearch, Logstash, Kibana) - Open source configurable SIEM
ELK Stack is set of open-source products managed by Elastic company.  
**Elasticsearch** is a powerful engine for full-text search and data analysis, built on top of the open-source Apache Lucene project.  
**Logstash** acts as a log processing pipeline — it gathers data from multiple input sources, applies filtering or transformation, and then sends it to a supported destination like Elasticsearch. However, many modern ELK stack setups skip Logstash entirely, opting instead for lighter tools such as Fluentd, which also supports log collection and forwarding to Elasticsearch.  
**Kibana** provides the visualization layer — it lets users explore, analyze, and display data stored in Elasticsearch through an intuitive interface.  
Lastly, **Beats** are lightweight agents installed on edge systems that collect specific types of data and forward it to the rest of the stack. In this case I am going to set up winlogbeat from this category.  

Course of the exercise:  
  
Visited <a href="https://www.elastic.co/docs">Elasticsearch</a> documentation to deploy it locally on my Ubuntu VM. Installation process takes a few commands to execute:   
Importing the key of repository 
<pre><code>wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
</code></pre>  
Saving repository definition
<pre><code>echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/9.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-9.x.list</code></pre>  
Finally, installing packages:
<pre><code>sudo apt-get update && sudo apt-get install elasticsearch</code></pre>  
<pre><code>sudo apt-get update && sudo apt-get install logstash</code></pre>  
Kibana has a little different method:
<pre><code>curl -O https://artifacts.elastic.co/downloads/kibana/kibana-9.0.0-linux-x86_64.tar.gz  
curl https://artifacts.elastic.co/downloads/kibana/kibana-9.0.0-linux-x86_64.tar.gz.sha512 | shasum -a 512 -c -  
tar -xzf kibana-9.0.0-linux-x86_64.tar.gz  
cd kibana-9.0.0/</code></pre>

#### ELK Stack Configuration: 
Before running services and expecting them to work perfectly, there is need to configure some of the files. The first one we are looking for is **elasticsearch.yml**  
![elasticsearch.yml location](configfile1.png)  
User must ensure to make Elasticsearch visible in network, to do so the variable **network.host** must be changed to *0.0.0.0*  
Lists of hosts to perform discovery is set by default to ["127.0.0.1", "[::1]"] this should not conflict but during troubleshooting I changed this value to *["127.0.0.1"]*  
Disable **xpack.security** ! it disallows connecting for some reason and will cause a lot of problems when trying to connect to the localhost port.  
You can compare your configuration to my configfiles attached to this directory.  

