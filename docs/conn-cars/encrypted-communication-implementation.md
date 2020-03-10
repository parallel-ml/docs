# Verified Communication Implementation

The code for our implementation is located in the following repository: https://github.com/nimashoghi/v2v-node

## Getting Started

### Development Dependencies and Pre-requisities
If you're planning on making changes to the code, make sure you have the following installed:
- Node.js
- Yarn
- Python 3
- OpenCV

If you simply want to use this, you can use our Docker images.

### Cloning the repository
```bash
git clone https://github.com/nimashoghi/v2v-node.git
cd v2v-node
yarn
```

### Generating private key public key pairs
First of all, you're going to need to generate private/public key pairs. If you have cloned the repository as shown above, you can run the following command to generate a key pair:
```bash
yarn ts-node ./scripts/create-key-pair.ts
```

### Building Docker Images
You can build the client Docker image by running
```bash
chmod +x ./build.sh
./build.sh
```
This will create the following Docker image: `niamshoghi/dac-v2v:latest`. You can change this name by editing `build.sh`.

### Deploying on the Server
To enable messaging between the Pis, you will need to setup the MQTT broker on the server. To do this, you will need to:
- Download the server's `docker-compose.yml` and `mosquitto.conf` files by running the following commands:
```bash
wget https://raw.githubusercontent.com/nimashoghi/v2v-node/master/deployment/server/docker-compose.yaml
wget https://raw.githubusercontent.com/nimashoghi/v2v-node/master/deployment/server/mosquitto.conf
```
- Run `docker-compose up -d` to start the server.
- Keep the IP of this server for the client deployment process.

### Deploying on the Pi
To deploy the program on the Pi, go through the following steps:
- Download the `docker-compose.yml` configuration onto each Pi. Modify the `docker-compose.yml` file so that the `MQTT_HOST` is the IP of the server that you deployed the MQTT broker on (from the previous step). Also modify the `ALL_TOPICS` variable so that it contains a comma-delimited list of the client ids for each Pi. The `CLIENT_ID` is a unique id that repesents the name of the MQTT channel for that Pi.
- Make sure that the public and private keys for the Pi are located at the following locations, respectively: `./keys/{{CLIENT_ID}}/public.pem` and `./keys/{{CLIENT_ID}}/private.pem`.
- Run the following command on the Pi to start the app: `CLIENT_ID={{CLIENT_ID}} docker-compose up -d`.
- The network should add each Pi into the system as it connects.

### Testing
Once everything is set up, you can:
- Set up two Roombas, one behind the other, so that the one in the back can see the QR code of the one in the front.
- Send movement commands to the front Roomba, using [our modified keyboard control script](https://github.com/nimashoghi/v2v-node/blob/master/keyboard-control/app.py). The setup instructions are the same as [the iRobot keyboard control instructions on the parallel-ml docs](https://parallel-ml.github.io/docs/#irobot/keyboard/).
