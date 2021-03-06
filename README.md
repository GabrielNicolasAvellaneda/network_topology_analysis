<h3>Network Topology Analysis</h3>
This repo contains the code used to collect network topology data (using a traceroute script), the <a href="http://spark.apache.org/">Apache Spark</a> code used for real-time analysis, and the <a href="https://zeppelin.apache.org/">Zeppelin</a> notebook used for data visualization.
<br>
<br><b>Background:</b>
<br>
<br>A network topology consists of many nodes (or hosts) and edges (connections) that link each of the nodes. In communication systems, there are typically many routes that we can take to get from Point A to Point B. 
<br>
<br>For example, if you are on your home wifi (Point A) and you request a webpage from Google.com (Point B), then your request will be relayed through many hosts along the route. Each time you make the request, a slightly different path may be used based on how the network is optimized, timeouts, failed nodes, etc. 
<br>
<br>For communication providers, these failed nodes create a problem. Isolating and resolving the issue is critical, since a failed node reduces performance, may cause downtime, cost money if a truck or person needs to manual troubleshoot a node, etc.
<br>
<br>This example focuses on a network topology for telecom, but the process and technology can be extended to any use case that involves a topology or hierarchy of information that needs to be analyzed in real-time.
<br>
<br><b>Technology Stack:</b>
<br>
<br>&bull; <a href="https://kafka.apache.org/">Apache Kafka</a>: Streams in the real-time health status of each device in the network topology.
<br>&bull; <a href="http://spark.apache.org/">Apache Spark</a>: Spark Streaming was used to process and analyze the device health status in real-time. I also maintained the device state, or current health status, of all devices using <a href="https://databricks.com/blog/2016/02/01/faster-stateful-stream-processing-in-apache-spark-streaming.html">mapWithState</a> so that recompute and roll-ups / aggregations could be performed quickly on the real-time stream. 
<br>&bull; <a href="https://hbase.apache.org/">Apache HBase</a>: The NoSQL distributed database, where all health status values are stored. This enables real-time read/write access to large database tables. 
<br>&bull; <a href="https://phoenix.apache.org/">Apache Phoenix</a>: Phoenix is the SQL interface to HBase, allowing SQL syntax to be used on top of the NoSQL DB.
<br>&bull; <a href="https://zeppelin.apache.org/">Apache Zeppelin</a>: Browser-based code editor and visualization tool (see screenshots below). Zeppelin has many interpreters, or code plugins, that enable a variety of languages/protocols to be used. These include Python, Spark, HBase, JDBC, Hive, Angular, etc. For this example, Angular was used to produce the Google Maps, by leveraging Zeppelin's front-end Angular API and some tricks (thanks <a href="http://randerzander.github.io/#?post=data-apps.md">Randerzander</a>) used to bind several backend JS variables to a globally accessible object.
<br>
<br><b>To run this project:</b>
<br>
<br>1. Clone this repo
<br>2. Navigate to the docker directory
<br>3. Execute ```./run.sh``` (You'll need to have <a href="https://www.docker.com/">Docker</a> installed on your machine)
<br>4. Enter the Zeppelin container bash (```docker exec -it zeppelin bash```)
<br>5. cd SparkNetworkAnalysis
<br>6. Build the Spark streaming project (```/apache-maven-3.3.9/bin/mvn clean package```)
<br>7. Start the Spark streaming project (```/spark/bin/spark-submit --master local[*] --class "SparkNetworkAnalysis" --jars /phoenix-spark-4.8.1-HBase-1.1.jar target/SparkStreaming-0.0.1.jar phoenix.dev:2181 mytestgroup dztopic1 1 kafka.dev:9092```)
<br>8. Start the Kafka stream, which will simulate the heath status for each device (```docker exec kafka python stream_kafka.py```)
<br>9. View the results as a Google Map within Zeppelin (also run interactive queries on data stored in HBase, via Phoenix)
<br>&ensp;&ensp;&ensp;&bull; Open your browser and go to http://localhost:8079/
<br>&ensp;&ensp;&ensp;&bull; Select the "Dashboard" notebook
<br>&ensp;&ensp;&ensp;&bull; Run the notebook, and enter in new IP addresses (POI) as desired.
<br>
<br><b>Screenshot #1: </b>Zeppelin notebook screenshot showing the user-input, where IP addresses (or points of interest) can be entered within the Zeppelin. This input is fed into a Spark job that fetches the data from HBase, performs data processing, then feeds the results to angular where it is rendered within Google Maps.
<br><img src="images/zeppelin_screenshot1.png" class="inline"/>
<br>
<br>
<br><b>Screenshot #2: </b>Zeppelin notebook screenshot showing the IP traceroute from my home wifi in Raleigh to Google.com servers (in Mountain View, CA). 
<br><img src="images/zeppelin_screenshot2.png" class="inline"/>
<br>
<br><b>References:</b>
<br>&bull; <a href="http://zeppelin.apache.org/docs/latest/displaysystem/front-end-angular.html">Apache Zeppelin - Angular (front-end API)</a>
<br>&bull; <a href="http://zeppelin.apache.org/docs/latest/displaysystem/back-end-angular.html">Apache Zeppelin - Angular (back-end API)</a>
<br>&bull; <a href="http://randerzander.github.io/#?post=data-apps.md">Randerzander's Data Apps in Zeppelin</a>
<br>&bull; <a href="https://databricks.com/blog/2016/02/01/faster-stateful-stream-processing-in-apache-spark-streaming.html">Apache Spark - mapWithState</a>
