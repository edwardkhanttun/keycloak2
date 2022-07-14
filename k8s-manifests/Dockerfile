FROM quay.io/keycloak/keycloak:latest as builder
ENV KC_HEALTH_ENABLED=true
ENV KC_METRICS_ENABLED=true
ENV KC_FEATURES=token-exchange
ENV KC_DB=mysql
# Install custom providers
RUN curl -sL https://github.com/aerogear/keycloak-metrics-spi/releases/download/2.5.3/keycloak-metrics-spi-2.5.3.jar -o /opt/keycloak/providers/keycloak-metrics-spi-2.5.3.jar
RUN /opt/keycloak/bin/kc.sh build

FROM quay.io/keycloak/keycloak:latest
COPY --from=builder /opt/keycloak/ /opt/keycloak/
WORKDIR /opt/keycloak
# for demonstration purposes only, please make sure to use proper certificates in production instead
RUN keytool -genkeypair -storepass password -storetype PKCS12 -keyalg RSA -keysize 2048 -dname "CN=server" -alias server -ext "SAN:c=DNS:auth-k3s-dis.mct.local,IP:172.20.20.105" -keystore conf/server.keystore
# change these values to point to a running postgres instance
ENV KC_DB_URL=jdbc:mysql://keycloak-db-mysql:3306/keycloak
ENV KC_DB_USERNAME=root
ENV KC_DB_PASSWORD=astar
ENV KC_HOSTNAME=auth-k3s-dis.mct.local
ENTRYPOINT ["/opt/keycloak/bin/kc.sh", "start"]
