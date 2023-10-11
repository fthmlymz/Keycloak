ARG KEYCLOAK_VERSION

FROM quay.io/keycloak/keycloak:$KEYCLOAK_VERSION as builder

ENV KC_DB=mssql
ENV KC_FEATURES="token-exchange,scripts,preview"

WORKDIR /opt/keycloak

RUN /opt/keycloak/bin/kc.sh build --cache=ispn --health-enabled=true --metrics-enabled=true --transaction-xa-enabled=false

FROM quay.io/keycloak/keycloak:$KEYCLOAK_VERSION

LABEL image.version=$KEYCLOAK_VERSION

COPY --from=builder /opt/keycloak/ /opt/keycloak/

# localhost SSL
# COPY /certs/aspnetapp.pfx /etc/x509/https/tls.pfx



# host docker internal
#COPY /certs/docker-host.crt /usr/local/share/ca-certificates/docker-host.crt
#COPY /certs/docker-host.key /usr/local/share/ca-certificates/docker-host.key

COPY /certs/docker-host.crt /etc/x509/https/docker-host.crt
COPY /certs/docker-host.key /etc/x509/https/docker-host.key
# host docker internal


USER root
RUN sed -i '/disabledAlgorithms/ s/ SHA1,//' /etc/crypto-policies/back-ends/java.config
USER keycloak

RUN /opt/keycloak/bin/kc.sh show-config

ENTRYPOINT ["/opt/keycloak/bin/kc.sh"]
