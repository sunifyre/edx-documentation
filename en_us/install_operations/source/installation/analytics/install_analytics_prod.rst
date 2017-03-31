.. _Installing the Open edX Analytics Stack in Production

#################################################
Installing Open edX Analytics in Production
#################################################

This page describes how to set up the analytics stack in a small scale production-like setup,
all on one box, with no use of AWS-specific features. The setup is mostly automated using ansible,
but still has a few pieces that are manual.

.. note::

   This is a "production-like" setup, but requires some tweaking to be truly production quality.
   For example: we strongly recommend you use HTTPS instead of HTTP for both insights and
   the LMS in production environments. These instructions do not yet contain complete instructions
   for setting up the system using HTTPS, but there is some stuff in here to help get you started.

.. note::

     Installation advice
     Don't run it on an existing edx platform. Yarn nodemanager will collide with xqueue service
     on port 8040. Tasks will be submitted but they will never be processed.
     For reference see: 
     yarn nodemanager default port -> https://goo.gl/1uy6kx 
     xqueue default port -> https://goo.gl/Ra5g3R.
     
.. _Installation Overview:

******************************
Installation Overview
******************************

#. Set up a new box and ensure it can connect to the LMS and the LMS mysql DB
#. Run ansible to install all the things and do most of the configuration
#. Manually finish a few bits of configuration (in particular, OAuth config on the LMS side)
#. Copy over tracking logs and run some test jobs
#. Automate loading of tracking logs and schedule jobs to run regularly


.. _Installation Script:

*****************************
Installation Script
*****************************

Below is a script to install all the things.

.. note::

   #. It expects to find a tracking.log file in the home directory – put an LMS log there before your run this.
   #. You'll need to manually run the OAuth management command on your LMS system – see below.
   #. You may need to do some network config to make sure your machines have the right ports open. See below.

Run the following on a new Ubuntu 12.04 box as a user that has sudo priveleges.

   .. code-block:: bash

      #!/bin/bash
      LMS_HOSTNAME="https://mulby.sandbox.edx.org"
      INSIGHTS_HOSTNAME="http://127.0.0.1:8110"  # Change this to the externally visible domain and scheme for your Insights install, ideally HTTPS
      DB_USERNAME="read_only"
      DB_HOST="localhost"
      DB_PASSWORD="password"
      DB_PORT="3306"
      # Run this script to set up the analytics pipeline
      echo "Assumes that there's a tracking.log file in \$HOME"
      sleep 2
      
      echo "Create ssh key"
      ssh-keygen -t rsa -f ~/.ssh/id_rsa -P ''
      echo >> ~/.ssh/authorized_keys # Make sure there's a newline at the end
      cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
      # check: ssh localhost "echo It worked!" -- make sure it works.
      echo "Install needed packages"
      sudo apt-get update
      sudo apt-get install -y git python-pip python-dev libmysqlclient-dev
      sudo pip install virtualenv
      echo 'create an "ansible" virtualenv and activate it'
      virtualenv ansible
      . ansible/bin/activate
      git clone https://github.com/edx/configuration.git
      
      cd configuration/
      pip install -r requirements.txt
      cd playbooks/edx-east/
      echo "running ansible -- it's going to take a while"
      ansible-playbook -i localhost, -c local analytics_single.yml --extra-vars "INSIGHTS_LMS_BASE=$LMS_HOSTNAME INSIGHTS_BASE_URL=$INSIGHTS_HOSTNAME"
      
      echo "-- Set up pipeline"
      cd $HOME
      sudo mkdir -p /edx/var/log/tracking
      sudo cp ~/tracking.log /edx/var/log/tracking
      sudo chown hadoop /edx/var/log/tracking/tracking.log
      
      echo "Waiting 70 seconds to make sure the logs get loaded into HDFS"
      # Hack hackity hack hack -- cron runs every minute and loads data from /edx/var/log/tracking
      sleep 70
      
      # Make a new virtualenv -- otherwise will have conflicts
      echo "Make pipeline virtualenv"
      virtualenv pipeline
      . pipeline/bin/activate
      
      echo "Check out pipeline"
      git clone https://github.com/edx/edx-analytics-pipeline
      cd edx-analytics-pipeline
      make bootstrap
      # HACK: make ansible do this
      cat <<EOF > /edx/etc/edx-analytics-pipeline/input.json
      {"username": $DB_USERNAME, "host": $DB_HOST, "password": $DB_PASSWORD, "port": $DB_PORT}
      EOF
      
      echo "Run the pipeline"
      # Ensure you're in the pipeline virtualenv
      remote-task --host localhost --repo https://github.com/edx/edx-analytics-pipeline --user ubuntu --override-config $HOME/edx-analytics-pipeline/config/devstack.cfg --wheel-url http://edx-wheelhouse.s3-website-us-east-1.amazonaws.com/Ubuntu/precise --remote-name analyticstack --wait TotalEventsDailyTask --interval 2016 --output-root hdfs://localhost:9000/output/ --local-scheduler
      
      echo "If you got this far without error, you should try running the real pipeline tasks listed/linked below"

.. _Installation Details:

***********************
Installation Details
***********************

#. Gather information:
    a. url to your LMS. e.g. lms.mysite.org
    b. url and credentials to your LMS DB. e.g. mysql.mysite.org
#. Create a box to use for the analytics stack. e.g. analytics.mysite.org.
    a. We started with a blank ubuntu 12.04 AMI on AWS (NOTE: there are known issues upgrading to 14.04 –
        changed package names, etc. They are probably easily solvable, but we haven't done it yet)
    b. Ensure that this box can talk to the LMS via HTTP
       ::
        curl lms.mysite.org

    c. Ensure that this box can connect to the DB:
       ::
        telnet mysql.mysite.org 3306

    d. Ensure that the box has the following ports open:
       ::
        80 -- for insights (actually 18110 at the moment -- should be changed)

    e. Install git and python tools:
       ::
        sudo apt-get update
        sudo apt-get install git
        sudo apt-get install python-pip
        sudo apt-get install python-dev
        sudo pip install virtualenv

    f. Create a new virtual environment
       ::
        virtualenv ansible
        . ansible/bin/activate

#. Run ansible to set up most of the services. This script will do the following:

   a. Install and configure hadoop, hive and sqoop
   b. Configure SSH daemon on the hadoop master node
   c. Configure the result store database
      
    1. Setup databases
    2. Setup Users
       
   d. Configure data API
      
    1. Shared secret
    2. Database connection
       
   e. Configure Insights
      
    1. API shared secret
    2. Tell insights where the LMS is
       
   The script:
   ::
    git clone https://github.com/edx/configuration.git
    cd configuration/
    pip install -r requirements.txt
    cd playbooks/edx-east/
    ansible-playbook -i localhost, -c local analytics_single.yml --extra-vars "INSIGHTS_LMS_BASE=mysite.org"
    # (If your site uses https, change the scheme and set the oauth flag to true. Enforce_secure means "insist on https".)
    # wait for a while
