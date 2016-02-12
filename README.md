# Overview

This interface layer handles the communication between a Oozie client and Oozie.

# Usage

## Provides

The Oozie deployment is provided by the Oozie charm. this charm has to
signal to all its related clients that has become available.

The interface layer sets the following state as soon as a client is connected:

  * `{relation_name}.related` The relation between the client and Oozie is established.

The Oozie provider can signal its availability through the following methods:

  * `set_installed()` Oozie is available.

  * `clear_installed()` Oozie is down.

An example of a charm using this interface would be:

```python
@when('oozie.started', 'client.related')
def client_present(client):
    client.set_installed()


@when('client.related')
@when_not('oozie.started')
def client_should_stop(client):
    client.clear_installed()
```


## Requires

This is the side that a Oozie client charm (e.g., Zeppelin)
will use to be informed of the availability of Oozie.

The interface layer will set the following state for the client to react to, as
appropriate:

  * `{relation_name}.related` The client is related to Oozie and is waiting for Oozie to become available.

  * `{relation_name}.available` Oozie is ready to be used.

An example of a charm using this interface would be:

```python
@when('zeppelin.installed', 'oozie.available')
@when_not('zeppelin.started')
def configure_zeppelin(oozie):
    hookenv.status_set('maintenance', 'Setting up Zeppelin')
    zepp = Zeppelin(get_dist_config())
    zepp.start()
    set_state('zeppelin.started')
    hookenv.status_set('active', 'Ready')


@when('zeppelin.started')
@when_not('oozie.available')
def stop_zeppelin():
    zepp = Zeppelin(get_dist_config())
    zepp.stop()
    remove_state('zepplin.started')
```


# Contact Information

- <bigdata@lists.ubuntu.com>

