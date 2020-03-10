# Setting Up MQTT-Based Communication Between Pis
We use MQTT to set up a single-publisher, multiple-subscriber event system where:
- All Raspberry Pis connect to the same MQTT broker.
- Each Raspberry Pi can broadcast a message to other Raspberry Pis over the MQTT network

## Code
The code is located at the following GitHub repository: https://github.com/parallel-ml/robot-communication

## Getting Started

### Client
On the client, run `python app.py --broker "mqtt://{IP_FOR_BROKER}" --label "{LABEL}" --topics-publish {PUBLISH TOPICS} --topics-subscribe {SUBSCRIBE TOPICS}` to start the Python test client.

### Broker
Clone the repository and run `docker-compose up -d` to start the MQTT server.
