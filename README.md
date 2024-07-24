
# ELK_kibana_metricbeat_Installation_Guide<br  />
﻿1.**Installing Elasticsearch, Kibana, and Metricbeat on AlmaLinux 8.6**<br  />
   1. ## **Prerequisites**<br  />
      Ensure your system is up-to-date:<br  />
      *sudo dnf update -y*<br  />
   1. ## **Install Elasticsearch**<br  />
      1. ### **Import the Elasticsearch GPG key:**<br  />
         *sudo rpm --import <https://artifacts.elastic.co/GPG-KEY-elasticsearch>*<br  />
      1. ### **Create a repository file for Elasticsearch:**<br  />
         *sudo tee /etc/yum.repos.d/elasticsearch.repo <<EOF [elasticsearch] name=Elasticsearch repository baseurl=<https://artifacts.elastic.co/packages/7.x/yum> gpgcheck=1 gpgkey=<https://artifacts.elastic.co/GPG-KEY-elasticsearch> enabled=1 autorefresh=1 type=rpm-md EOF*<br  />
      1. ### **Install Elasticsearch:**<br  />
         *sudo dnf install elasticsearch -y*<br  />
      1. ### **Enable and start the Elasticsearch service:**<br  />
         *sudo systemctl enable elasticsearch sudo systemctl start elasticsearch*<br  />
      1. ### **Verify Elasticsearch installation:**<br  />
         *curl -X GET "[localhost:9200/](http://localhost:9200/)"*<br  />
   1. ## **Install Kibana**<br  />
      1. ### **Create a repository file for Kibana:**<br  />
         *sudo tee /etc/yum.repos.d/kibana.repo <<EOF [kibana] name=Kibana repository baseurl=<https://artifacts.elastic.co/packages/7.x/yum> gpgcheck=1 gpgkey=<https://artifacts.elastic.co/GPG-KEY-elasticsearch> enabled=1 autorefresh=1 type=rpm-md EOF*<br  />
      1. ### **Install Kibana:**<br  />
         *sudo dnf install kibana -y*<br  />
      1. ### **Enable and start the Kibana service:**<br  />
         *sudo systemctl enable kibana sudo systemctl start kibana*<br  />
      1. ### **Verify Kibana installation:**<br  />
         *Open a web browser and go to [http://localhost:5601](http://localhost:5601/).*<br  />
   1. ## **Install Metricbeat**<br  />
      1. ### **Create a repository file for Metricbeat:**<br  />
         *sudo tee /etc/yum.repos.d/metricbeat.repo <<EOF [elastic-7.x] name=Elastic 7.x baseurl=<https://artifacts.elastic.co/packages/7.x/yum> gpgcheck=1 gpgkey=<https://artifacts.elastic.co/GPG-KEY-elasticsearch> enabled=1 autorefresh=1 type=rpm-md EOF*
      1. ### **Install Metricbeat:**<br  />
         *sudo dnf install metricbeat -y*<br  />
      1. ### **Enable and start the Metricbeat service:**<br  />
         *sudo systemctl enable metricbeat sudo systemctl start metricbeat*<br  />
   1. ### **Configure Metricbeat:**<br  />
         *sudo metricbeat modules enable system sudo metricbeat setup*<br  />
      1. ### **Verify Metricbeat installation:**<br  />
         *sudo systemctl status metricbeat*<br  />

         **How to acess ?**

         To access Elasticsearch, Kibana, and Metricbeat from a browser using the IP address 103.151.111.242, you'll need to configure the services to listen on the IP address instead of [localhost](http://localhost/) (127.0.0.1). Here’s how you can do it:
         
   1. ### **Configure Elasticsearch to Listen on IP Address**
         
         **Edit the Elasticsearch configuration file:**

         *sudo nano /etc/elasticsearch/elasticsearch.yml*

         **Find the line that starts with [network.host](http://network.host/) and Update it to:**

         makefile

         Copy code

         [network.host](http://network.host/): 103.151.111.242

         **Save and close the file.**

         **Restart Elasticsearch for the changes to take effect:**

         *sudo systemctl restart elasticsearch*

         **Check Elasticsearch Connection:**

         **Ensure that Metricbeat can connect to Elasticsearch by testing the connection:**

         *curl -X GET "[http://103.151.111.242:9200](http://103.151.111.242:9200/)"*
         
   1. ### **Configure Kibana to Connect to Elasticsearch**
         
         **Edit the Kibana configuration file:**

         sudo nano /etc/kibana/kibana.yml

         **Find the line that starts with [server.host](http://server.host/) and Update it to:**

         server.host: "0.0.0.0"

         elasticsearch.hosts: ["[http://103.151.111.242:9200](http://103.151.111.242:9200/)"]

         **Add or modify the logging settings:**

         logging:

         verbose: true

         **Optionally, configure the Kibana section to use Kibana for setup:**

         setup.kibana: host: "[http://103.151.111.242:5601](http://103.151.111.242:5601/)"

         ssl.enabled: false

         timeout: 60s

         **Save and close the file.**

         **Restart Kibana for the changes to take effect:**

         *sudo systemctl restart kibana*

         **Check Kibana Connection:**

         **Test the connection to Kibana:**

         curl -X GET "[http://103.151.111.242:5601](http://103.151.111.242:5601/)"
         
   1. ### **Configure Metricbeat configuration file to specify Elasticsearch as the output:**
      
         sudo nano /etc/metricbeat/metricbeat.yml

         **Find the Elasticsearch output section (usually towards the end of the file) and configure it like this:**

         output.elasticsearch:

         hosts: ["103.151.111.242:9200"]

         setup.ilm.overwrite: true

         **Save and close the file.**

         **Enable and Start Metricbeat Enable the Metricbeat service to start on boot:**

         *sudo systemctl enable metricbeat*

         **Start the Metricbeat service:**

         *sudo systemctl start metricbeat*

         **Verify Metricbeat Operation Check the status of Metricbeat to ensure it is running without errors:**

         *sudo systemctl status metricbeat*

      **Configure Metricbeat Modules (optional) Metricbeat comes with modules that collect specific metrics. You can enable modules based on your needs. For example, to enable the system module:**

         *sudo metricbeat modules enable system*

         **Then, load the dashboards and start Metricbeat:**

         *sudo metricbeat setup*

         *sudo systemctl restart metricbeat*

         **Accessing Metricbeat Data in Kibana After Metricbeat starts sending data to Elasticsearch, you can visualize and analyze the data in Kibana. Access Kibana using** [**http://103.151.111.242:5601/**](http://103.151.111.242:5601/) **in your web browser.**

         **Adjust Elasticsearch Configuration:**

         Edit the Elasticsearch configuration file (/etc/elasticsearch/elasticsearch.yml) and ensure the following settings are correctly configured:

         cluster.name: my-cluster

         node.name: node-1

         network.host: 103.151.111.242

         discovery.seed\_hosts: ["103.151.111.242"]

         cluster.initial\_master\_nodes: ["node-1"]
         1. #### **Disable Bootstrap Checks (Temporary)**
      
            **As a temporary measure for troubleshooting, you can disable bootstrap checks in elasticsearch.yml:**

            bootstrap.memory\_lock: false

            **Restart Elasticsearch**

            *sudo systemctl restart elasticsearch*
            
         1. #### **Verify Elasticsearch Status**
            
            **Restart Elasticsearch**
            
            *sudo systemctl status elasticsearch*
