# Thingy91x - Exercise
Sensor + MQTT sample + TLS Modem tracing in wireshark

https://docs.nordicsemi.com/bundle/ncs-latest/page/nrf/samples/net/mqtt/doc/description.html

https://github.com/nrfconnect/sdk-nrf/tree/main/samples/net/mqtt

## Goals of exercise
 - Run the NCS NET MQTT sample on the Thingy91x
 - Modify the sample to sample sensor data
 - Observe in the MQTT server that the device is sending data
 - Trace TLS traffic using modem traces with a dev security tag

### Building and running
 1. Navigate to the MQTT sample in NCS (nrf/samples/net/mqtt)
 2. Go to src/modules/sampler and replace the function _sample()_ with the current function:

```sample
static void sample(void)
{
	// something
}
```
 3. Navigate to the overlay file overlay-tls-nrf91.conf and set the security tag in the dev tag range (2147483648– 2147483667):
```
CONFIG_MQTT_HELPER_SEC_TAG=2147483648
```
 4. Build and run the sample with the TLS overlay and modem trace snippet:
```
west build -p -b thingy91x/nrf9151/ns -- -DEXTRA_CONF_FILE="overlay-tls-nrf91.conf" -Dapp_SNIPPET=nrf91-modem-trace-uart
```
 5. Flash the firmware 
```
west thingy91x-dfu
```

### Expected log output

```
*** Booting Zephyr OS build v3.2.99-ncs2-34-gf8f113382356 ***
[00:00:00.286,254] <inf> network: Bringing network interface up and connecting to the network
[00:00:00.286,621] <dbg> mqtt_helper: mqtt_state_set: State transition: MQTT_STATE_UNINIT --> MQTT_STATE_DISCONNECTED
[00:00:00.310,028] <dbg> mqtt_helper: mqtt_helper_poll_loop: Waiting for connection_poll_sem
[00:00:04.224,426] <inf> network: Network connectivity established
[00:00:09.233,612] <dbg> mqtt_helper: broker_init: Resolving IP address for test.mosquitto.org
[00:00:10.541,839] <dbg> mqtt_helper: broker_init: IPv6 Address found 2001:41d0:1:925e::1 (AF_INET6)
[00:00:10.541,900] <dbg> mqtt_helper: mqtt_state_set: State transition: MQTT_STATE_DISCONNECTED --> MQTT_STATE_TRANSPORT_CONNECTING
[00:00:13.747,406] <dbg> mqtt_helper: mqtt_state_set: State transition: MQTT_STATE_TRANSPORT_CONNECTING --> MQTT_STATE_TRANSPORT_CONNECTED
[00:00:13.747,467] <dbg> mqtt_helper: mqtt_state_set: State transition: MQTT_STATE_TRANSPORT_CONNECTED --> MQTT_STATE_CONNECTING
[00:00:13.747,497] <dbg> mqtt_helper: client_connect: Using send socket timeout of 60 seconds
[00:00:13.747,497] <dbg> mqtt_helper: mqtt_helper_connect: MQTT connection request sent
[00:00:13.747,558] <dbg> mqtt_helper: mqtt_helper_poll_loop: Took connection_poll_sem
[00:00:13.747,558] <dbg> mqtt_helper: mqtt_helper_poll_loop: Starting to poll on socket, fd: 0
[00:00:13.747,589] <dbg> mqtt_helper: mqtt_helper_poll_loop: Polling on socket fd: 0
[00:00:14.370,727] <dbg> mqtt_helper: mqtt_evt_handler: MQTT mqtt_client connected
[00:00:14.370,788] <dbg> mqtt_helper: mqtt_state_set: State transition: MQTT_STATE_CONNECTING --> MQTT_STATE_CONNECTED
[00:00:14.370,788] <inf> transport: Connected to MQTT broker
[00:00:14.370,819] <inf> transport: Hostname: test.mosquitto.org
[00:00:14.370,849] <inf> transport: Client ID: 350457791735879
[00:00:14.370,880] <inf> transport: Port: 8883
[00:00:14.370,880] <inf> transport: TLS: Yes
[00:00:14.370,910] <dbg> mqtt_helper: mqtt_helper_subscribe: Subscribing to: F4CE37111350/my/subscribe/topic
[00:00:14.494,354] <dbg> mqtt_helper: mqtt_helper_poll_loop: Polling on socket fd: 0
[00:00:15.136,047] <dbg> mqtt_helper: mqtt_evt_handler: MQTT_EVT_SUBACK: id = 2469 result = 0
[00:00:15.136,077] <inf> transport: Subscribed to topic F4CE37111350/my/subscribe/topic
[00:00:15.136,108] <dbg> mqtt_helper: mqtt_helper_poll_loop: Polling on socket fd: 0
[00:00:15.136,260] <dbg> mqtt_helper: mqtt_evt_handler: MQTT_EVT_PUBLISH, message ID: 52428, len = 850
[00:00:15.136,444] <inf> transport: Received payload: Test message from mosquitto_pub! on topic: F4CE37111350/my/subscribe/topic
[00:00:15.136,444] <dbg> mqtt_helper: mqtt_helper_poll_loop: Polling on socket fd: 0
[00:00:44.495,147] <dbg> mqtt_helper: mqtt_helper_poll_loop: Polling on socket fd: 0
[00:00:45.478,210] <dbg> mqtt_helper: mqtt_evt_handler: MQTT_EVT_PINGRESP
```
### Modem tracing using the celluar monitor was covered in the session with Stig Bjørlykke

### Optional - Subscribe to topic and observe data being received, alternatives:
Subscribe to the MQTT topic that the device sends data to and observe that the data is sent to the broker.

1. Online MQTT client: https://mqttx.app/web-client#/recent_connections
2. MQTT client extensions in VScode - There are various
3. Mosquitto CLI:

```
mosquitto_sub -h test.mosquitto.org -t 355025930003742/my/publish/topic
```
