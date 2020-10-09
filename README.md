# remote stage control demo

Demo for real-time control with xy-stage devices via web browser. 
You should have use a MQTT broker. Both stage device and web interface will communicate through this centralized server.

# MQTT Topic and Payload Design

In MQTT, the word topic refers to a string that the broker uses to filter messages for each connected client. 

A stage client named **myStage** gets current position from the stage device and publishes status information on the topic **stage/info/myStage** 
with a JSON encoded message payload like **{"status": {"isConnected": "false", "isRunning": "false"}, "position": {"x_world": 0.0, "y_world": 0.0}}**.
A web interface for **myStage** receives information on the topic and updates interface without reload.
The web interface publishes stage control commands on the topic **stage/ctrl/myStage** with message payload of **{"command":"GOTO","d_x":"35.893","d_y":"139.954"}**.
The stage client receives commands on the topic and control the stage device.

# setup
    > git clone https://gitlab.misasa.okayama-u.ac.jp/DREAM/vs-remote.git
    > cd vs-remote
    > cp docker-compose.yml.example docker-compose.yml
    > docker-compose up -d


# access to the web interface

    > python -m webbrowser http://localhost/simple.html?topic=myStage


# monitoring communications between stage device and web interface. 

    > docker-compose logs -f stage
    Attaching to vs-remote_stage_1
    stage_1      | 2020-10-09 04:00:49,092 INFO:publish message {"status": {"isConnected": "false", "isRunning": "false"}, "position": {"x_world": 0.0, "y_world": 0.0}} on topic stage/info/myStage
    stage_1      | 2020-10-09 04:00:49,093 INFO:published: 1000
    stage_1      | 2020-10-09 04:00:50,096 INFO:Received message 'b'{"command":"GOTO","d_x":"35.893","d_y":"139.954"}'' on topic 'stage/ctrl/myStage' with QoS 0
    stage_1      | 2020-10-09 04:00:51,100 INFO:publish message {"status": {"isConnected": "false", "isRunning": "false"}, "position": {"x_world": 35.893, "y_world": 139.954}} on topic stage/info/myStage
    stage_1      | 2020-10-09 04:00:51,100 INFO:published: 1001

    > mosquitto_sub -h localhost -t stage/info/myStage