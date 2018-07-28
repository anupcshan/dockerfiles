# Modified from https://github.com/eclipse/mosquitto/blob/0bb602ed7a89da80d25e6b959a130fdcf0556be5/docker/1.4.12/Dockerfile

FROM alpine:3.7

LABEL Description="Eclipse Mosquitto MQTT Broker"

RUN apk --no-cache add mosquitto=1.4.15-r0 && \
    mkdir -p /mosquitto/config /mosquitto/data /mosquitto/log && \
    echo -en "bind_address 0.0.0.0\nport 1883\n" > /mosquitto/config/mosquitto.conf && \
    chown -R mosquitto:mosquitto /mosquitto

EXPOSE 1883
CMD ["/usr/sbin/mosquitto", "-c", "/mosquitto/config/mosquitto.conf", "-v"]
