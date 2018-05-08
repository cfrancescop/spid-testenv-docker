# Identity Server
FROM openjdk:8
LABEL MAINTAINER Francesco Pirrone <francesco.pirrone@finance-evolution.com>
RUN mkdir /spid-testenvironment && \
    curl -o /spid-testenvironment/spid-testenv-identityserver.tar.gz https://codeload.github.com/italia/spid-testenv-identityserver/tar.gz/docker-compose && \
    mkdir /spid-testenvironment/is && \
    tar -zxvf /spid-testenvironment/spid-testenv-identityserver.tar.gz -C /spid-testenvironment/is --strip-components=1 && \
    rm -f /spid-testenvironment/spid-testenv-identityserver.tar.gz

# Set custom conf
RUN mv /spid-testenvironment/is/spid-confs/conf/conf/carbon.xml /spid-testenvironment/is/identity-server/repository/conf/ && \
    mv /spid-testenvironment/is/spid-confs/conf/conf/claim-config.xml /spid-testenvironment/is/identity-server/repository/conf/ && \
    mv /spid-testenvironment/is/spid-confs/conf/bin/wso2server.sh /spid-testenvironment/is/identity-server/bin/

# Port exposed
EXPOSE 9443

RUN chmod +x /spid-testenvironment/is/identity-server/bin/wso2server.sh

WORKDIR /spid-testenvironment/is/identity-server

ENTRYPOINT ["bin/wso2server.sh"]