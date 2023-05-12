## Custom Tab Plugin

This is a prototype Morpheus plugin which demonstrates how to use
the Morpheus Plugin framework's custom tab provider to query and 
display data from a custom table in Morpheus. 

In a fictional scenario, Company XYZ wishes to show a list of installed
services associated with each Instance. Data is created and inserted via a 
workflow (not provided) but can be viewed (and deleted) from an
Instance's custom tab.

<img width="1240" alt="image" src="https://github.com/spoonboy-io/custom-tab-plugin/assets/7113347/c31e07b1-2149-45e2-84e2-5d228ebd73dc">

### For learning

This code demonstrates techniques, which are not guaranteed to be the best approaches, but which achieve the following:

* Create a custom instance tab using the tab provider
* Render data from a custom table in the tab
* Replicate Morpheus UI HTML controls & style on the tab
* Implement a controller to handle delete requests, controller has code to parse the querystring since only GET is supported
* Make writes to the database (better a different database) via a new connection (plugin framework offers read only)
* Implements JavaScript code in the view to send data to the controller
* Is secure AFAIK. Uses role permissions, and also checks nonce for replay

### Getting started

There are database migration files provided to create a custom table and seed that table with dummy data.
Follow these steps to load the data to your Morpheus appliance database (or external database).

Create database table.

```
mysql -h hostname -u morpheus morpheus < /path/to/migrations/create_managed_service_plugin_table.sql
```
Seed the database table with dummy data.

```
mysql -h hostname -u morpheus morpheus < path/to/migrations/seed_managed_service_plugin_table.sql
```

**NOTE**: Dummy data is available for an instance with name `DummyTechService-01`. 
Instances with other names will show the tab, but report no managed services. 
Create an instance with the above name, to show the dummy data.

### Database user
This plugin uses a separate database connection for the purpose of deleting data from the custom table.
Currently there are no configuration options in the plugin to set this data. For now, amend the [connection details for the user here](https://github.com/spoonboy-io/custom-tab-plugin/blob/main/src/main/groovy/com/morpheusdata/managedServiceTab/managedServiceTabPluginController.groovy#L32), before building the plugin, or create the user with these credentials. Configuration options to enable this data to be maintained in Morpheus will follow.

### Build the plugin

Build the plugin locally with the included gradle wrapper.

```
./gradlew make
```

The compiled plugin will be available in the `./plugin` directory. 

Upload the plugin to Morpheus (admin > integrations > plugins tab)

Log out/in to Morpheus to see the tab on existing instances.

### Create a release

!Note this is work-in-progess!

Releases can be automatically created on Github by pushing a version tag
in the format `vx.x.x`.

To this end there is a `Makefile` with a `release` command to assist with this.

### Versions

The plugin has been configured and tested with Morpheus v.6.1.0

Plugin builds are tested/performed with Java v16

### Todo/Ideas
* Provisioning Workflow to attach services to an Instance
* The config for the additional database connection needs to go in the plugin settings
* Amend controller response so as to include new metrics to update the stats after deleting (count, total cost, avg cost, max cost), and update the DOM in JS
