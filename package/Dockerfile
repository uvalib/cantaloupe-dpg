#
# build the target container
#
FROM openjdk:11.0.11-9-jre-buster

# update the packages
RUN apt update

# Create the run user and group
RUN addgroup --gid 1000 webservice && adduser -q --uid 1000 --gid 1000 --disabled-login webservice

# set the timezone appropriatly
ENV TZ=UTC
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Specify home 
ENV APP_HOME /cantaloupe-uva
WORKDIR $APP_HOME

# Create necessary directories
RUN mkdir -p $APP_HOME/scripts $APP_HOME/bin

# Update permissions
RUN chown -R webservice $APP_HOME /home/webservice && chgrp -R webservice $APP_HOME /home/webservice

# port and run command
EXPOSE 8080
CMD scripts/entry.sh

# Move in necessary assets
COPY package/scripts/entry.sh $APP_HOME/scripts/entry.sh
COPY package/config/cantaloupe.properties $APP_HOME/bin
COPY package/data/container_bash_profile /home/webservice/.profile

# download the cantaloupe release
RUN wget -c https://github.com/cantaloupe-project/cantaloupe/releases/download/v5.0.2/cantaloupe-5.0.2.zip && unzip cantaloupe-5.0.2.zip && rm cantaloupe-5.0.2.zip
RUN mv cantaloupe-5.0.2/cantaloupe-5.0.2.jar $APP_HOME/bin && ln -s $APP_HOME/bin/cantaloupe-5.0.2.jar $APP_HOME/bin/cantaloupe.jar

# Ensure permissions are correct
RUN chown webservice:webservice /home/webservice/.profile $APP_HOME/scripts/entry.sh && chmod 755 /home/webservice/.profile $APP_HOME/scripts/entry.sh

# Add the build tag
ARG BUILD_TAG
RUN test -n "$BUILD_TAG" && touch $APP_HOME/buildtag.build-$BUILD_TAG || touch $APP_HOME/buildtag.build-0

# Specify the user
USER webservice

#
# end of file
#
