# Energenie Pi Control

Now works with Amazon Echo :)  (https://github.com/thenom/Alexa-Energenie)

This is a Django project a provides a basic control webpage for current state info and main socket control buttons.  It is designed for use with the Raspberry Pi and the Energenie Pi Hat (ENER314\ENER314-RT).  The default is to use a local SQLLite DB file but this can be changed (https://docs.djangoproject.com/en/1.9/ref/settings/#databases).

It checks the schedules at an interval set by the variable CHECKINTER in the run_config file (see setup below) and modifies the state of the socket accordingly.  You can apply a random deviation the the schedule to mimic variations in turning the sockets on and off.  For example, if a schedule is set to turn a socket on at 16:30 and has a random minute value of 10 minutes then the light will turn on between 16:20 and 16:40.

The on\off time can be controlled by the following day time based on a selected city: manually(default), dawn, dusk, sunrise, sunset, noon.  This uses the [astral](https://pypi.python.org/pypi/astral/0.8.1) python module to control the times.

* Credit to https://github.com/goinnn/django-multiselectfield for the multiselectfield

Basic useful feature list:

 * Setup and modify sockets for control
 * Setup multiple schedules and control multiple sockets via each schedule
 * Add multiple time slots to each schedule
 * Basic web page for control and sockets current status
 * Randomly changes the on\off time based on a deviation value in minutes
 * Can set the on\off time based on sunrise\sunset\etc...

# Dependencies

 * Django 1.8
 * Raspberry Pi with Networking
 * Energenie Raspberry Pi Hat (ENER314\ENER314-RT)   # See Note1
 
```
yum install MySQL-python
yum install python-devel
pip install django==1.8.7
pip install astral
```

# Screenshot

![Alt text](/screenshot.png?raw=true "Control page")



# Running

I have created a startup script for this as there are 2 processes required for this to fully function but it is basic and doesn't control the django server properly as it spawns other subprocesses and the main process ends:
```
./run.sh
```
If you want to manually run the 2 services then run these from the energenie directory:

Django server - This provides the main django framwork admin page and the basic web management page
```
$ python manage.py runserver 0.0.0.0:8000
```
Scheduling python service - this is just a basic python loop that calls the schedule check view in the main django powersocket app:
```
$ python sched_run.py
```

To access the control page:
\<host\>:8000/powersocket

To access the admin pages:
\<host\>:8000/admin

# Setup
You will need to modify the run script to match your own working directory.  To get this to work on boot in my setup i just placed this script in rc.local, adding the & at the end to force it to run in the background.  You will also need to modify the DATABASES details in energenie_pi/settings.py to match your own MySQL\SQLite server setup.

You will also need to change your 'ALLOWED_HOSTS' in energenie_pi/settings.py to the URL you are calling for your local setup.  This is required if you set DEBUG to false in the same file.

you will need to place a file in the main directory with your run configuration settings.  An example is shown below:

```
[root@rpi-energenie energenie-pi]# cat run_config 
CWD="/usr/local/energenie-pi/"
PYTHON=/usr/bin/python
BINDADDR=0.0.0.0
PORT=8000
CHECKINTER=10
```

# Notes:

1. A Rapberry Pi or the Energenie Hat is not required as the code catches this and disables the control for testing purposes.
