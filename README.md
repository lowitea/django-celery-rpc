django-celery-rpc
=================

Remote access from one system to models and functions of other one using Celery machinery.

Relies on three outstanding python projects:

 - [Celery](http://www.celeryproject.org/)
 - [Django Rest Framework](http://www.django-rest-framework.org/)
 - [Django](https://www.djangoproject.com/)

## Main features

Client and server are designed to:

 - filter models with Django ORM lookups;
 - change model state (create, update, update or create, delete);
 - change model state in bulk mode (more than one object per request);
 - call function;

## Configure

Default configuration of *django-celery-rpc* must be overridden in settings.py by *CELERY_RPC_CONFIG*.
The *CELERY_RPC_CONFIG* is a dict which must contains at least two keys: *BROKER_URL* and *CELERY_RESULT_BACKEND*.
Any Celery config params also permitted
(see [Configuration and defaults](http://celery.readthedocs.org/en/latest/configuration.html))

### server *span*

setting.py:

	# minimal required configuration
	CELERY_RPC_CONFIG = {
		'BROKER_URL': amqp://10.9.200.1/,
		'CELERY_RESULT_BACKEND': 'redis://10.9.200.2/0',
	}

### server *eggs*

setting.py:

	# alternate request queue and routing key
	CELERY_RPC_CONFIG = {
		'BROKER_URL': amqp://10.9.200.1/,
		'CELERY_RESULT_BACKEND': amqp://10.9.200.1/',
		'CELERY_DEFAULT_QUEUE': 'celery_rpc.requests.alter_queue',
        'CELERY_DEFAULT_ROUTING_KEY': 'celery_rpc.alter_routing_key'
	}

### client

setting.py:

	# this settings will be used in clients by default
	CELERY_RPC_CONFIG = {
		'BROKER_URL': amqp://10.9.200.1/,
		'CELERY_RESULT_BACKEND': 'redis://10.9.200.2/0',
	}

	# 'eggs' alternative configuration will be explicitly passed to the client constructor
	CELERY_RPC_EGGS_CLIENT = {
		# BROKER_URL will be used by default from section above
		'CELERY_RESULT_BACKEND': amqp://10.9.200.1/',
		'CELERY_DEFAULT_QUEUE': 'celery_rpc.requests.alter_queue',
        'CELERY_DEFAULT_ROUTING_KEY': 'celery_rpc.alter_routing_key'
    }

example.py

	from celery_rpc.client import Client
	from django.conf import settings

	# create client with default settings
	span_client = Client()

	# create client for `eggs` server
	eggs_client = Client(CELERY_RPC_EGGS_CLIENT)

## Run server instance

	celery worker -A celery_rpc.app

For daemonization see [Running the worker as a daemon](http://celery.readthedocs.org/en/latest/tutorials/daemonizing.html)

## TODO

 - Set default non-generic model serializer.
 - Send alternate model serializer in request.
 - Django-free mode for client and server.