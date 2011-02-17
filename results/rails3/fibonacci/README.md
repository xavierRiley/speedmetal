# Install Gems
    jruby -S gem install bundler
    cd speedmetal/apps/rails3/fibonacci
    jruby -S bundle install
    gem install bundler
    bundle install


# TorqueBox Setup

## Create TorqueBox deployment descriptor
    rm $JBOSS_HOME/server/default/deploy/*-knob.yml
    cat << EOF > $JBOSS_HOME/server/default/deploy/fibonacci-knob.yml
    ---
    application:
      root: /home/ec2-user/speedmetal/apps/rails3/fibonacci/
      env: production
    web:
      context: /
    EOF
## Start TorqueBox
    $JBOSS_HOME/bin/run.sh -b 0.0.0.0


# Passenger Setup

## Copy app to /tmp
Passenger needs the nginx workers that run as the 'nobody' user
to have access to the application.
    rm -rf /tmp/fibonacci/
    cp -r speedmetal/apps/rails3/fibonacci/ /tmp/

## Start Passenger
    RACK_ENV=production passenger start -p 8080


# Thin Setup

## Start Thin
    cd speedmetal/apps/rails3/fibonacci/
    thin -p 8080 -e production start


# Trinidad Setup

## Start Trinidad
    cd speedmetal/apps/rails3/fibonacci/
    jruby -S trinidad -r -p 8080 -e production -t