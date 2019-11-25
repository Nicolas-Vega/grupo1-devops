# imagen base
FROM openjdk:8-jre-alpine

# variables de entorno para la imagen
ENV SPRING_OUTPUT_ANSI_ENABLED=ALWAYS \
    JHIPSTER_SLEEP=15 \
    JAVA_OPTS=""

# Agrego el WAR
ADD *.jar /home/app.jar
# copio la carpeta config, en donde tengo los certificados de AFIP
#COPY certs /home/config/certs

# actualizo las properties, indicando la nueva ruta de los certificados
#ENV APPLICATION_AFIP_P12FILE  /home/config/certs/marce/keystore.p12
#ENV APPLICATION_AFIP_TRUSTSTORE /home/config/certs/afip/homo/wsaahomo.jks

#ENV TZ America/Argentina/Buenos_Aires

# puerto que se expone
EXPOSE 8080

# ejecucion del war
CMD echo "The application will start in ${JHIPSTER_SLEEP}s..." && \
    sleep ${JHIPSTER_SLEEP} && \
    java ${JAVA_OPTS} -Djava.security.egd=file:/dev/./urandom -Dspring.profiles.active=prod -jar /home/app.jar
