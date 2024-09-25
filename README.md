# Thingy91x - Exercise
Sensor + MQTT sample + TLS Modem tracing in wireshark

https://docs.nordicsemi.com/bundle/ncs-latest/page/nrf/samples/net/mqtt/doc/description.html

https://github.com/nrfconnect/sdk-nrf/tree/main/samples/net/mqtt

## Goals of exercise
 - Run the NCS NET MQTT sample on the Thingy91x
 - Modify the sample to sample and send sensor data to a MQTT broker
 - Receive messages from the device on a MQTT topic
 - Trace and decrypt TLS traffic using modem traces

### Building and running
 1. Navigate to the MQTT sample in NCS (nrf/samples/net/mqtt). _VSCode extension?_: Click on "Browse samples" and select the nRF SDK MQTT sample.
 2. Open up a "nRF Connect Terminal"
 3. Modify the sample according to changes in the following commit:

https://github.com/nrfconnect/sdk-nrf/commit/4c3c48fb14cfb6ce8922a179a667fdcd10572dbb

 * Updates the sample to use a dev tag (to decrypt TLS traffic in wireshark)
 * Updates the sampler.c module to sample temperature
 * Updates DT to use the BME680

If you don't want to do this manually you can pull in the changes with the following commands:
```
git remote add simen https://github.com/simensrostad/fw-nrfconnect-nrf
git remote update
git cherry-pick 4c3c48fb14cfb6ce8922a179a667fdcd10572dbb

or

git fetch ncs refs/pull/17472/head && git checkout FETCH_HEAD
git cherry-pick 4c3c48fb14cfb6ce8922a179a667fdcd10572dbb

```
 3. Build and run the sample with the TLS overlay and modem trace snippet using the terminal:
```
west build -p -b thingy91x/nrf9151/ns -- -DEXTRA_CONF_FILE="overlay-tls-nrf91.conf" -Dapp_SNIPPET=nrf91-modem-trace-uart
```
 5. Flash the firmware:
```
west thingy91x-dfu
```
Serial flashing is not currently supported in the VSCode extension!
In order to flash you need to create a new terminal window in VSCode and run the west DFU command there.

### Expected log output

```
*** Booting nRF Connect SDK v2.7.0-5cb85570ca43 ***
*** Using Zephyr OS v3.6.99-100befc70c74 ***
[00:00:00.298,675] <inf> network: Bringing network interface up and connecting to the network
[00:00:00.557,159] <inf> nrf_modem_lib_trace: Trace thread ready
[00:00:00.565,307] <inf> nrf_modem_lib_trace: Trace level override: 2
[00:00:02.898,406] <inf> network: Network connectivity established
[00:00:09.596,801] <inf> transport: Connected to MQTT broker
[00:00:09.596,862] <inf> transport: Hostname: test.mosquitto.org
[00:00:09.596,923] <inf> transport: Client ID: 355025930003742
[00:00:09.596,923] <inf> transport: Port: 8883
[00:00:09.596,954] <inf> transport: TLS: Yes
[00:00:09.597,015] <inf> transport: Subscribing to: 355025930003742/my/subscribe/topic
[00:00:09.764,373] <inf> transport: Subscribed to topic 355025930003742/my/subscribe/topic
[00:00:53.669,006] <inf> transport: Published message: "Hello MQTT! Current temperature is: 27.860000" on topic: "355025930003742/my/publish/topic"
[00:01:00.312,377] <inf> transport: Published message: "Hello MQTT! Current temperature is: 29.020000" on topic: "355025930003742/my/publish/topic"
[00:02:00.312,561] <inf> transport: Published message: "Hello MQTT! Current temperature is: 29.220000" on topic: "355025930003742/my/publish/topic"
[00:03:00.304,168] <inf> transport: Published message: "Hello MQTT! Current temperature is: 30.600000" on topic: "355025930003742/my/publish/topic"
[00:04:00.312,927] <inf> transport: Published message: "Hello MQTT! Current temperature is: 31.500000" on topic: "355025930003742/my/publish/topic"
[00:05:00.313,110] <inf> transport: Published message: "Hello MQTT! Current temperature is: 32.170000" on topic: "355025930003742/my/publish/topic"
```
### Trace TLS traffic using Cellular Monitor - Should be plug and play - This was covered by Stig.

### Optional - Subscribe to topic and observe data being received, alternatives:
Subscribe to the MQTT topic that the device sends data to and observe that the data is sent to the broker.

1. Online MQTT client: https://mqttx.app/web-client#/recent_connections
2. MQTT client extensions in VScode - There are various
3. Mosquitto CLI:

```
mosquitto_sub -h test.mosquitto.org -t 355025930003742/my/publish/topic
```
