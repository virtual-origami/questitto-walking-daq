# questitto
Mosquitto + Telegraf + QuestDB Data Acquisition Stack used to for acquiring data for Pedestrian Walking Pattern

## Components

Hardware and Software components used to conduct Experiments:

### Hardware

- __M5StickC Development Kit with Hat from M5Stack__

- __Real-Time Kinematics (RTK) High Precision GPS__

- __Raspberry Pi 4 Model B__

### Software

- Since [QuestDB](https://questdb.io) does not have an `ARM64` supported Docker Image, the DB is hosted on a local server

    - QuestDB UI: `http://<SERVER_IP>:9000`
    - TCP Port for Data Ingestion: `tcp://<SERVER_IP>:9009`

- [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) for connecting to MQTT Broker and ingesting data into QuestDB

- [Mosquitto MQTT Broker](https://mosquitto.org/) available on __Raspberry Pi 4 Model B__: `tcp://<PI_IP_ADDRESS>:1883`


## Running the DAQ Stack

1. Create a Docker Network using:

        docker network create iotstack


2. OPTIONAL: If you need basic Authentication on the MQTT Broker/Client then enable the settings in the `mosquitto/config` file and set
   passwords and usernames accordingly on the `mosquitto/config/passwd` file. To encrypt the passwords execute:

        docker run -it --rm -v $(pwd)/mosquitto/config:/mosquitto/config eclipse-mosquitto mosquitto_passwd -U /mosquitto/config/passwd

    Also uncomment the settings in the `telegraf.toml` to let telegraf connect to the MQTT broker with authentication as well as the `.env` file
    with appropriate credentials

3. Change the environment variable `TG_QUESTDB_SOCKET` in the `questitto.env` file for ingesting data into QuestDB

4. Adapt the MQTT Topics accordingly in the `telegraf.toml` file

5. Bring the Stack up on the Raspberry Pi using:

         USER_ID="$(id -u)" GRP_ID="$(id -g)" docker-compose -f docker-compose.yml up

## Debug / Logs

Check logs using:

    docker-compose -f docker-compose.yml logs -f mosquitto

    docker-compose -f docker-compose.yml logs -f telegraf