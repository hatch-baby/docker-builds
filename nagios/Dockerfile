FROM jasonrivers/nagios:4.5.12
#       4.4.14 is from ubunutu:22.04
#FROM jasonrivers/nagios:4.4.6   # branch is missing from https://github.com/JasonRivers/Docker-Nagios/tree/master, 4.4.4 was 16.04, 4.4.7 was 20.04

LABEL maintainer="Ken DeLong <ops@hatch.co>"
LABEL org.opencontainers.image.source="https://github.com/kenwdelong/docker-builds/tree/master/nagios"
LABEL org.opencontainers.image.description="Container with Nagios and Boto"

RUN apt update
RUN apt-get --yes install vim &&\
    apt-get clean all
    
RUN pip3 install boto boto3 botocore --break-system-packages
    

RUN echo 'SetEnv TZ "America/Los_Angeles"' >> /etc/apache2/conf-enabled/nagios.conf

# user id is from all.yml. Nagios was 999
# from https://www.cyberciti.biz/faq/linux-change-user-group-uid-gid-for-all-owned-files/
RUN usermod -u 3002 nagios       &&\
    find / -user 999 ! -path "/proc/*" -exec chown -h nagios {} \;
    #chown -R nagios /opt/nagios  &&\
    #chown -R nagios /orig
    