# Test Kafka

## Run kafka

Change host IP in docker-compose.yml

```bash
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://<YOU_HOST_IP>:9092
```

Start docker-compose

```bash
docker-compose up
```

Install kafkalib for python

```bash
pip install kafka-python
```

```bash
# run producer
python3 producer.py
# then run consumer, while producer is running 
python3 consumer.py
```

If everything works - kafka broker is successfully running

## Run producer from Jetson

Install librdkafka

```bash
cd /opt/nvidia/deepstream/deepstream/sources/libs/kafka_protocol_adaptor
sudo git clone https://github.com/edenhill/librdkafka.git
cd librdkafka
sudo git reset --hard 7101c2310341ab3f4675fc565f64f0967e135a6a
sudo ./configure
sudo make
sudo make install
sudo cp /usr/local/lib/librdkafka* /opt/nvidia/deepstream/deepstream/lib/
sudo ldconfig
sudo apt-get install libglib2.0 libglib2.0-dev
sudo apt-get install  libjansson4  libjansson-dev
```

Now librdkafka is installed, we will start out deepstream-application with proper configuration.

```bash
cd /opt/nvidia/deepstream/deepstream/sources/apps/sample_apps/deepstream-test4
sudo make
./deepstream-test4-app -i /opt/nvidia/deepstream/deepstream/samples/streams/sample_720p.h264 \
    -p /opt/nvidia/deepstream/deepstream/lib/libnvds_kafka_proto.so \
    --conn-str="<ADD-YOUR-HOST-IP-HERE>;9092" \
    --topic="test" -s 0
```

Then run `consumer.py` to receive messages

```bash
python3 consumer.py
```

**RECEIVED MESSAGE EXAMPLE:**

```json
{
  'messageid': '7a565011-09f1-4702-a41c-f24e163e4511',
  'mdsversion': '1.0',
  '@timestamp': '2021-10-08T21:28:18.237Z',
  'place': {
    'id': '1',
    'name': 'XYZ',
    'type': 'garage',
    'location': {
      'lat': 30.32,
      'lon': -40.55,
      'alt': 100.0
    },
    'aisle': {
      'id': 'walsh',
      'name': 'lane1',
      'level': 'P2',
      'coordinate': {
        'x': 1.0,
        'y': 2.0,
        'z': 3.0
      }
    }
  },
  'sensor': {
    'id': 'CAMERA_ID',
    'type': 'Camera',
    'description': '"Entrance of Garage Right Lane"',
    'location': {
      'lat': 45.293701447,
      'lon': -75.8303914499,
      'alt': 48.1557479338
    },
    'coordinate': {
      'x': 5.2,
      'y': 10.1,
      'z': 11.2
    }
  },
  'analyticsModule': {
    'id': 'XYZ',
    'description': '"Vehicle Detection and License Plate Recognition"',
    'source': 'OpenALR',
    'version': '1.0'
  },
  'object': {
    'id': '-1',
    'speed': 0.0,
    'direction': 0.0,
    'orientation': 0.0,
    'vehicle': {
      'type': 'sedan',
      'make': 'Bugatti',
      'model': 'M',
      'color': 'blue',
      'licenseState': 'CA',
      'license': 'XX1234',
      'confidence': -0.10000000149011612
    },
    'bbox': {
      'topleftx': 564,
      'toplefty': 484,
      'bottomrightx': 603,
      'bottomrighty': 513
    },
    'location': {
      'lat': 0.0,
      'lon': 0.0,
      'alt': 0.0
    },
    'coordinate': {
      'x': 0.0,
      'y': 0.0,
      'z': 0.0
    }
  },
  'event': {
    'id': '56daca47-3690-4a8a-bd5d-0bc0c02ec7a3',
    'type': 'moving'
  },
  'videoPath': ''
}
```