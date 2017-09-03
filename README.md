# MQTT load balancing with HAProxy

In IoT applications a lot of devies are connected to the same backend e.g. via MQTT. Even strong MQTT broker have their limits according to max connections. Therefore load balancing incoming MQTT TCP connections can be a solution to distribute the connection over multiple brokers. This example shows how to setup an MQTT load balancer with [HAProxy](http://www.haproxy.org/) and Docker. [Moquette](https://github.com/andsel/moquette) is used as MQTT broker, but you could also replace it with any other broker.

## Running the example

Prerequisites:
* Docker
* Docker Compose

Build HAProxy docker image and start it together with broker instances:

```
git clone https://github.com/dnobel/mqtt-load-balancer.git
cd mqtt-load-balancer/haproxy-lb
docker build -t haproxy-lb .
cd ..
docker-compose up 
```

Goto http://localhost:8080 and observe incoming connections.

Connect with multiple MQTT clients and see how they will be load balanced between the MQTT instances.

## HAProxy configuration

HAProxy is a perfect HTTP/TCP proxy and load balancer and can be easily configured. We build our own docker image by inheriting from the HAProxy image and providing our own [config file](haproxy-lb/haproxy.cfg). The config file defines a stats pages and the TCP proxy.

HAProxy not only load balances the incoming TCP connections, it also monitors the MQTT brokers by checking for an open port. If one MQTT broker instance goes down, HAProxy redirects all incoming connections to the second broker, until the first one comes up again.