# Django-ELK-Stack


#### Abstract
This is short tutorial to integrate django logs to ELK stack where ELK stands for Elasticsearch , Logstash and Kibana.
Logs are live streamed to elasticsearch via logstash and logs data is visible on kibana.

#### Background
The ELK stack is a popular open-source software suite used for centralized logging, log analysis, and real-time data visualization. ELK stands for Elasticsearch, Logstash, and Kibana, which are the three main components of the stack.

Elasticsearch is a distributed search and analytics engine that provides scalable and efficient search capabilities, while Logstash is a data processing pipeline that helps to collect, parse, and transform data from various sources before feeding it into Elasticsearch. Kibana is a powerful data visualization tool that allows users to explore, analyze, and visualize data stored in Elasticsearch.

Together, these three tools form a powerful and flexible platform for managing large volumes of data and gaining valuable insights from it. The ELK stack is widely used in various industries, including IT, finance, healthcare, and e-commerce, to monitor systems and applications, detect anomalies, and troubleshoot issues.


#### How to bring up ELK stack
1. Clone repo `https://github.com/deviantony/docker-elk` .
2. Follow the instructions given `https://github.com/deviantony/docker-elk/blob/main/README.md`
3. Bring up the containers with `docker-compose up` , with this command the logs from the containers will also be visible in the screen or you can run the containers in detached mode.
4. This shall bring up the 3 containers for elasticsearch , kibana and logstash.
5. Now kibana can be accessed on port `localhost:5601`
6. All the logs that are being sent to elasticsearch are indexed and index name shall be given or it will be given by default by elasticsearch.

#### Debugging on ELK stack
1. Logs for logstash can be seen in docker with following command `docker logs <logstash container id>`.
2. All the data that has been indexed on elasticsearch can be seen on kibana dev tools with following command `GET /_cat/indices`  
3. If index is being default created by the elasticsearch it will be visible after the above command.
4. Logs from the index <ds-logs-generic-default-2023.05.04-000001> can be seen with the help of following command `GET /.ds-logs-generic-default-2023.05.04-000001/_search?pretty`


#### Sending logs from django application.

1. Assuming your django is running after the following command `python3 manage.py runserver 8500`.
2. Install the python-logstash module with `pip3 install python-logstash`
3. Update logger settings in settings.py of django application.
```
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'simple': {
            'format': '[%(asctime)s] %(levelname)s %(message)s',
            'datefmt':'%Y-%m-%dT%H:%M:%S'
        }
    },
    'handlers': {
        'file': {
            'class': 'logging.FileHandler',
            'filename': 'warning.log',
            'level': 'WARNING',
            'formatter': 'simple'
        },
        'logstash': {
            'level': 'WARNING',
            'class': 'logstash.TCPLogstashHandler',
            'host': 'localhost',
            'port': 50000,
            'version': 1,
            'message_type': 'django',
            'tags': ['django'],
        },
    },
    'loggers': {
        '': {
            'handlers': ['file', 'logstash'],
            'level': 'WARNING',
            'propagate': True,
        },
    },
}
```

Above settings save the logs in a seperate file named warning.log and also sends the logs to logstash.
All the logs that are set as of level- WARNING, will only be saved to file and sent to logstash.
All the logs are sent via TCP protocol to logstash on port 50000 also logs that are sent to kibana are streamed live to dashboard.

### How to set index in kibana for logs.
Explained in the video - https://www.youtube.com/watch?v=_EnyrFKF4K4

