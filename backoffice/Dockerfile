FROM openjdk:8
LABEL MAINTAINER Francesco Pirrone, francesco.pirrone@finance-evolution.com

# Node 6
RUN curl -sL https://deb.nodesource.com/setup_6.x -o nodesource_setup.sh && \
    bash nodesource_setup.sh && \
    apt-get install -y nodejs && \
    apt-get install -y build-essential

# Backoffice
RUN mkdir /spid-testenvironment && \
    curl -o /spid-testenvironment/spid-testenv-backoffice.tar.gz https://codeload.github.com/italia/spid-testenv-backoffice/tar.gz/master && \
    mkdir /spid-testenvironment/bo && \
    tar -zxvf /spid-testenvironment/spid-testenv-backoffice.tar.gz -C /spid-testenvironment/bo --strip-components=1 && \
    rm -f /spid-testenvironment/spid-testenv-backoffice.tar.gz  
# Build backoffice
RUN cd /spid-testenvironment/bo/backoffice && \
    npm install --suppress-warnings
WORKDIR /spid-testenvironment/bo/backoffice

RUN cd server && \
    npm install --suppress-warnings

# install dependecies
RUN apt-get install libxml2-utils && ls && cd idp_metadata && \
    curl -o xmlsectool-2.0.0-bin.zip https://shibboleth.net/downloads/tools/xmlsectool/2.0.0/xmlsectool-2.0.0-bin.zip && \
    unzip xmlsectool-2.0.0-bin.zip && \
    rm xmlsectool-2.0.0-bin.zip && \
    cd .. && \
    npm run build-metadata && \
    npm run build 

# build certs
RUN cd certs && \
    keytool -J-Duser.language=en -import -alias  'AgID'  \
        -trustcacerts -keystore wso2carbon.jks -file ca-crt.pem -storepass wso2carbon -noprompt && \
    openssl pkcs12 -export -name wso2carbon \
        -in spid-testenv-identityserver-crt.pem \
        -inkey spid-testenv-identityserver-key.pem \
        -password pass:wso2carbon -out keystore.p12 && \
    keytool -importkeystore -srckeystore keystore.p12 -destkeystore wso2carbon.jks \
        -srcstoretype pkcs12 -alias wso2carbon -srcstorepass wso2carbon -deststorepass wso2carbon \
        -destkeypass wso2carbon -noprompt && \
    keytool -list -keystore wso2carbon.jks -storepass wso2carbon
# change to keytool import
RUN cp /etc/ssl/certs/java/cacerts certs/client-truststore.jks

# multistage build,comment if your docker version doesn't support it from here

FROM node:8-slim

WORKDIR /root/
COPY --from=0 /spid-testenvironment/bo/backoffice .

# end multistage block

EXPOSE 8080
ENTRYPOINT ["npm", "run", "start-prod"]
