# DataTorrent Console (dtManage) Guide

The DataTorrent Console (aka dtManage) is a web-based user interface that allows you to monitor and manage the DataTorrent RTS platform and applications running on your Hadoop cluster.

To download the platform or the VM sandbox, go to [http://www.datatorrent.com/download](http://www.datatorrent.com/download).

![Console Screenshot](images/dtmanage/console-welcome-screen.png)

The Console includes the following features:

  * [AppFactory](#appfactory)
  * [Launch](#launch)
  * [Monitor](#monitor)
  * [Visualize](dtdashboard)
  * [Develop](#develop)
  * [Configure](#configure)

## AppFactory

The AppFactory hosts a collection of applications and templates grouped by various industries, that can be imported or downloaded (as .apa files). You can use the applications as they are, or use the templates as a starting point to develop custom applications.

![](images/dtmanage/console-appfactory.png)

## Launch

The Launch screen lists all of the imported and uploaded packages and their applications. Applications can be launched and active instances viewed.

![Launch](images/dtmanage/console-launch.png)

#### Launching Apps

To launch an app in an App Package, click on the launch button to the far right of the list. A dialog box will appear with several options: 

- **Specify a name for the running app**
  The console will pre-populate this field with an appropriate name, but you can specify your own name. Make sure it is unique among all the applications running in the Hadoop cluster of your DataTorrent installation.
- **Specify launch properties**
  In addition to choosing a config file, you may also directly specify properties in the launch pop-up by selecting this option. Any required properties will automatically show up in this section and require input. Note that there are several helpful functions when specifying custom properties:
  - *add* - App Packages can have custom properties applied at launch time to override existing properties.
    - *add default properties* - App Packages can also have default properties. This function will add the default properties to the list, making it easy for you to override the defaults. This button can be found clicking on the *add* button's submenu.
  If any properties were added, the option to save the properties as a Configuration Package is activated.
- **Use configuration file**
  App Package config files are xml files that contain `<properties>` that get interpreted and used for launching an application. To choose one, enable the check box and choose the config file you want to use for launch.
- **Use configuration package**
  App Packages with a separate associated Configuration Package can be selected here. The Configuration Package will launch with its own custom properties.
- **Specify the [scheduler queue](https://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)**
  This input allows you to specify which queue you want the application to be launched under. The default behavior depends on your Hadoop installation, but typically will be `root.[USER_NAME]`.
- **Enable [Garbage Collection logging](http://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/index.html)**
  Checking this box enables GC logging by including the `-Xloggc:<LOG_DIR>/gc.log -verbose:gc -XX:+PrintGCDateStamps` JVM options for all the containers of the application. This GC logging in turn enables garbage collection widgets described later.

![Launch app modal](images/dtmanage/console-launch-app.png)

> **Note:** For more information about config files and custom properties, see the [Application Packages Guide](https://www.datatorrent.com/docs/guides/ApplicationDeveloperGuide.html)


## Visualize

See the [dtDashboard](dtdashboard) page.


## Monitor

The Monitor section of the Console can be used to monitor, troubleshoot, and manage running application instances.

### Cluster Overview

The Cluster Overview page shows overall cluster statistics as well as a list of running DataTorrent applications.

![Cluster Overview Page](images/dtmanage/console-monitor-home.png)

The cluster statistics include some performance statistics and memory usage information. As for the application list, take note of: **ended apps** This includes all the ended applications that are still in the resource manager history.

### Instance Page

To get to an application instance page, click on either the app name or the app id in the list of running applications.

![Instance Page View](images/dtmanage/console-monitor-instance.png)

All sections and subsections of the instance page currently use a dashboard/widget system. The controls for this system are located near the top of the screen, below the breadcrumbs:

![widget controls](images/dtmanage/console-widget-ctrls.png)

There are tool tips to help you understand how to work with dashboards and widgets. For most users, the default dashboard configurations (*logical*, *physical*, *physical-dag-view*, *metric-view*, *attempts*) will suffice. The following is a list of widgets available on an app instance page:

#### Application Overview Widget

All the default dashboard tabs have this widget. It contains basic information regarding the app plus a few controls. To end a running application, use either the “shutdown” or “kill” buttons in this widget:

![shutdown and kill buttons](images/dtmanage/console-instance-kill-shutdown.png)

The “shutdown” function tries to gracefully stop the application, while “kill” forces the application to end. In either case, you will need to confirm your action.

You can also use the **set logging level** button on this widget to specify what logging level gets written to the dt.log files. 

![Application Overview widget](images/dtmanage/console-set-level-instance.png)

You will then be presented with a dialog where you can specify either fully-qualified class names or package identifiers with wildcards:

![set log level modal](images/dtmanage/console-set-level-modal.png)

#### Stram Events Widget

Each application has a stream of notable events that can be viewed with the StrAM Events widget:

![Stram Events](images/dtmanage/console-events.png)

Some events have additional information attached to it, which can be viewed by clicking the "i" icon in the list:

![Event Detail Modal](images/dtmanage/console-events-modal.png)


#### Logical DAG Widget

This widget visualizes the logical plan of the application being viewed:

![](images/dtmanage/logical-dag.png)

Additionally, you can cycle through various metrics aggregated by logical operator. In the screenshot above, processed tuples per second and emitted tuples per second are shown. 

>**Pro tip:** Hold the alt/option key while using your mouse scroll wheel to zoom in and out on the DAG.

#### Physical DAG Widget

This is similar to the Logical DAG Widget, except it shows the fully deployed "physical" operators. Depending on the partitioning of your application, this could be significantly more complex than the Logical DAG view.

![](images/dtmanage/physical-dag.png)

Same-colored physical operators in this widget indicates that these operators are in the same container.

#### Logical Operators List Widget

This widget shows a list of logical operators in the application. This table, like others, has live updates, filtering, column ordering, stacked row sorting, and column resizing. 

One nice feature specific to this widget is the ability to set the logging level for the Java class of a logical operator by selecting it in this list and using the provided dropdown, like so:

![](images/dtmanage/console-set-level-from-oplist.png)

#### Physical Operators List Widget

Shows the physical operators in the application.

#### Containers List Widget

Shows the containers in the application. From this widget you can: select a container and go to one of its logs, fetch non-running containers and view information about them, and even kill selected containers.

#### Logical Streams List Widget

Shows a list of the streams in the application. There are also links to the logical operator pages for the sources and sinks of each stream.

#### Metrics Chart

Shows various metrics of your application on a real-time line chart. Single-click a metric to toggle its visibility. Double-click a metric to toggle all other keys' visibility.

#### Garbage Collection (GC) Chart by Heap

This chart shows a container's heap memory in use in KB (kilo-bytes) against time. The chart is constructed by plotting and extrapolating in-use heap memory obtained from events in the GC log file of a container which requires GC logging to be enabled as described in [Launching apps](#launching-apps). The chart shown is for a single container that is selectable from the radio buttons shown at the top right corner of the widget. Each container in the radio buttons and the chart is color-coded with the same color. The containers included depend on the context of the widget:

- all application containers in the application view
- all the containers containing the physical partitions of a logical operator in the logical operator view
- the single parent container of a physical operator in the physical operator view
- the container itself in the selected container view

![](images/dtmanage/gc-chart-by-heap.png)

#### Garbage Collection (GC) Log Table

This table shows the garbage collection (GC) events for a group of containers. This table too requires GC logging to be enabled as described in [Launching apps](#launching-apps). The containers included in the group depend on the context of the widget:

- all application containers in the application view
- all the containers containing the physical partitions of a logical operator in the logical operator view
- the single parent container of a physical operator in the physical operator view
- the container itself in the selected container view

![](images/dtmanage/gc-log-table.png)

#### Garbage Collection (GC) Chart by Duration

This discrete bar chart shows GC event duration in seconds against time for a group of containers. Each bar is of fixed-width but the height denotes the duration of the corresponding GC event. This chart too requires GC logging to be enabled as described in [Launching apps](#launching-apps). One or more containers are selectable from the radio buttons shown at the top right corner of the widget. Each container in the radio buttons and the chart is color-coded with the same color. The containers included depend on the context of the widget:

- all application containers in the application view
- all the containers containing the physical partitions of a logical operator in the logical operator view
- the single parent container of a physical operator in the physical operator view
- the container itself in the selected container view

![](images/dtmanage/gc-chart-by-duration.png)

### Recording and Viewing Sample Tuples

There is a mechanism called tuple recording that can be used to easily look at the content of tuples flowing through your application. To use this feature, select a physical operator from the Physical Operators List widget and click on the “record a sample” button. This will bring up a modal window which you can then use to traverse the sample and look at the actual content of the tuple (converted to a JSON structure):

![](images/dtmanage/console-record-tuples.gif)

>**Pro tip:** Select multiple tuples by holding down the shift key.

### Viewing Logs

Another useful feature of the Console is the ability to view container logs of a given application. To do this, select a container from the Containers List widget (default location of this widget is in the “physical” dashboard). Then click the logs dropdown and select the log you want to look at:

![](images/dtmanage/console-log-viewing.gif)

Once you are viewing a log file in the console, there are few tricks to traversing it. You can scroll to the top to fetch earlier content, scroll to the bottom for later content, "tail" the log to watch for real-time updates, grep for strings in the selected range or over the entire log, and click the “eye” icon to the far left of every line to go to that location of the log:

![](images/dtmanage/console-log-viewing-adv.gif)



## Develop

Application packages and application configurations can be viewed and managed in the Develop section. For more information about application packages visit the [Application Packages Guide](http://docs.datatorrent.com/application_packages/).

![Development Tab](images/dtmanage/console-dev-screen.png)

### Application Packages

To access the application package listing, click on the "Apps" link from the Develop Tab index page. From here, you can perform several operations directly on application packages:

- Download the app package
- Delete the app package
- Launch applications in the app package

> **Note:** If authentication is enabled, you may not be able to see others’ app packages, depending on your permissions.

### Application Package Page

Once you have uploaded or imported an App Package, clicking on the package name in the list will take you to the Application Package Page, where you can view all the package details.

![Application Package Page](images/dtmanage/console-package.png)

Aside from various pieces of meta information (owner, DataTorrent version, required properties, etc), you will see a list of apps found in this package. 

### Viewing an Application

All DataTorrent applications are made up of operators that connect together via streams to form a Directed Acyclic Graph (DAG). To see a visualization of this DAG, click on the application name in the list of applications. In addition to the DAG, Package Properties and any Required Properties will be listed on this page.

![DAG View](images/dtmanage/console-dag-view.png)

## Configure

The RTS configuration menu is accessed by the cog button on the top-right corner of the Console. Under the **configuration** section, there are links to various tools to help you configure and troubleshoot your DataTorrent installation. The available menu items may differ depending on your security settings.

![](images/dtmanage/console-config-system-screen.png)

### System Information

This page displays details of the following:
* Gateway Information - Displays all the details of the gateway properties that are configured in the current DT RTS installation.
* Hadoop Information - Displays the details of Hadoop properties that are configured in the current DT RTS installation.
* Phone Home Information - Displays the data from the current DT RTS installation, such as usage statistics, which is dynamically aggregated over the time period of 24 hours and sent to the DT servers. The data is aggregated in total time frames that is it includes data from the start of the gateway installation to present.

![System Configuration Page](images/dtmanage/console-system-screen2.png)


### System Configuration

This page shows the system configuration, provides a way to make system changes, and displays any known issues for the DataTorrent RTS installation.

![System Configuration Page](images/dtmanage/console-system-screen1.png)

In addition, you can perform the following actions from this page:

  * SMTP Configuration - Set up SMTP to be able to send out email alerts and notifications.
  * Restart the Gateway - This button can be used to restart the gateway when the Hadoop configuration or system properties have changed.
  * Usage Reporting - If enabled, your DataTorrent installation will send various pieces of information such as bug reporting and usage statistics back to our servers.
  * Installation Wizard - Rerun the initial installation to reconfigure HDFS installation path and Hadoop executable.

### Security 

By default, your installation starts with no security enabled, which may be sufficient on a closed network with a limited set of users. However, it is recommended to use some form of authentication especially for production environments.

![Security Configuration Page](images/dtmanage/security-screen1.png)

DataTorrent RTS supports various authentication methods which can be enabled by following instructions in the [Authentication](dtgateway_security/#authentication) section.

### Services
Services represent global, shared, and automatically managed Docker or Apex processes. Applications are dependent on such services. From the Service page,  you can create new services, import packaged services, or manage installed services.

![Security Configuration Page](images/dtmanage/security-screen1.png)

### Alerts

System alerts can be configured to notify users through the Console and emails based on various system and application metrics.

![System Alerts page](images/dtmanage/system-alerts1.png)

Click on the `+ create new alert` button to create an alert.

![System Alerts page](images/dtmanage/system-alerts2.png)

An alert consists of

 * a condition (a JavaScript expression)
 * a list of recipient email addresses
 * a threshold value in milliseconds
 * a message, and
 * an enabled/disabled flag

The gateway periodically (every 5 seconds) processes all enabled alerts by evaluating the condition. If the condition evaluates to `true`, the alert is said to be "in effect".
If the condition evaluates to `false`, the alert is said to be "out" (or "out of effect"). If the alert stays "in effect" for the duration specified as the threshold value,
then the alert is triggered and the gateway sends an "in effect" email message to all the recipient email addresses.

If a triggered alert goes "out of effect" then the gateway immediately sends an "out of effect" email message to all the recipient email addresses.

The alert condition is specified as a JavaScript expression which is evaluated in the context of something called `topics` which are described [here](dtgateway_systemalerts/#alerts-and-topics).

The gateway also provides pre-defined alert "templates" that allow a user to create alerts for certain common conditions without having to write JavaScript expressions.

![System Alerts page](images/dtmanage/system-alerts3.png)

Click on the "Predefined Conditions" tab and select a template from the drop-down list. Depending on your selection, you will need to provide more values to be filled into the template.
As an example, for the "Application Memory Usage" template you need to provide the Application Name and Memory values as shown below:

![System Alerts page](images/dtmanage/system-alerts4.png)

You can click on the "Javascript Code" tab to see the generated JavaScript expression that corresponds to your alert template selection and provided values as shown below:

![System Alerts page](images/dtmanage/system-alerts5.png)

You can generate a test email to validate your alert by checking the "Send Test Email" check-box and clicking on the blue "Test" button. The test email is sent regardless of the true or false result
of the JavaScript condition, if the evaluation has no errors provided SMTP is configured as described in the Alerts section.

### License 

Use the License Information page to view how much of your DataTorrent license capacity your cluster is consuming as well as what capabilities your license permits. You can also upload new license files here.

![License Screen](images/dtmanage/console-license.png)

### User Profile

The User Profile page displays information about the current user, including their username, the authentication scheme being used, and the roles that the current user has. In addition, users can perform the following actions:

- Change password 
- Change the default home page
- Change the theme of the console
- Restore the default options of the console

![User Profile](images/dtmanage/console-profile.png)

### User Management

Use this page to manage users and their corresponding roles on your DataTorrent cluster. This page is accessible only to an admin user. You can do the following from this page:

*   Add users
*   Change users’ roles
*   Change users’ password
*   Delete users
*   Add roles
*   Edit role permissions
*   Delete roles

![User Management Screen](images/dtmanage/console-user-mgmt.png

> **Note:** With most authentication schemes, the admin role cannot be deleted.

### Installation Wizard

The first time you open the Console, after installing DataTorrent RTS on your cluster, it will take you to the Installation Wizard. This walks you through the initial configuration of your DataTorrent installation, by confirming the following:

* Location of the Hadoop executable
* DFS location where all the DataTorrent files are stored
* DataTorrent license
* Summary and review of any remaining configuration items

At any time, you can go back to the installation wizard from the Configuration Tab. It can help diagnose issues and reconfigure your cluster and gateway.

![](images/dtmanage/hadoop-config-screenshot.png)

When your Hadoop cluster has security enabled with Kerberos, there will be four additional controls in the installation wizard: 

- **Kerberos Principal**: The Kerberos principal (e.g. primary/instance@REALM) to use on behalf of the management console.
- **Kerberos Keytab**: The location (path) of the Kerberos keytab file to use on the gateway node's local file system.
- **YARN delegation token lifetime**: If the value of the `yarn.resourcemanager.delegation.token.max-lifetime` property in your cluster configuration has been changed from the default, enter it here. Otherwise, leave this blank and the default will be assumed.
- **Namenode delegation token lifetime**: If the value of the `dfs.namenode.delegation.token.max-lifetime` property in your cluster configuration has been changed from the default, enter it here. Otherwise, leave this blank and the default will be assumed.


> **Note:** The token lifetime values you enter will not actually set these values in your hadoop configuration, it is only meant to inform the DataTorrent platform of these values.




