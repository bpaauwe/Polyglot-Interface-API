
# Interface API
## The Interface events

`config` is triggered whenever there is a change in the configuration,
the nodes, the notices, anything. The config is passed in parameter. You
can check for config.isInitialConfig to know if the is the first config
received. Use this for initialization when you want to have a working config loaded.

The config object will have a property newParamsDetected set to true if the
customParams changed.

`poll` is triggered frequently, based on your short poll and long poll values.
The longPoll parameter is a flag telling you if this is a long poll or short poll.

`stop` is triggered whenever the node server is being stopped.

`delete` is triggered whenever the user is deleting the NodeServer.


The following events are less commonly used but could be useful for troubleshooting:

`messageReceived` is triggered for every messages received from Polyglot to the NodeServer.

`messageSent` is triggered for every messages sent to Polyglot from your NodeServer.

`mqttConnected` is the first event being triggered and happens when the MQTT connection is established. The config is
not yet available.

`mqttReconnect` the MQTT connection reconnected.

`mqttOffline` the MQTT connection went offline.

`mqttClose` the MQTT connection closed.

`mqttEnd` the MQTT connection ended.


## The Interface methods

start(), to initiate the MQTT connection and start communicating with Polyglot.

isConnected(), which tells you if this NodeServer and Polyglot are connected via
MQTT. Return is boolean.

addNode(node), Adds a new node to Polyglot. You fist need to instantiate a node
using your custom class, which you then pass to addNode. 

getConfig(), Returns a copy of the last config received. What is returned?  JSON data, JSON string?

getNodes(), Returns your list of nodes. This is not just an array of nodes
returned by Polyglot. This is a list of nodes with your classes applied to them.

getNode(address), Returns a single node.

delNode(node), Allows you to delete the node specified. You need to pass the
actual node. Alternatively, you can use delNode() directly on the node itself,
which has the same effect. Q: should this take a node or node address?

updateProfile(), Sends the latest profile to ISY from the profile folder.

getNotices(), Returns the current list of Polyglot notices.

addNotice(key, text), Adds a notice to the Polyglot UI. The key allows to refer
to that notice later on.

addNoticeTemp(key, text, delaySec), Adds a notice to the Polyglot UI. The notice
will be active for delaySec seconds.

removeNotice(key), Remove notice specified by the key.

removeNoticesAll(), Removes all notices from Polyglot.

getCustomParams(), Returns all the configuration parameters from the UI.

getCustomParam(key), Returns the param as seen in the UI.

saveCustomParams(params), Saves the params as specified by the params objects.
All the params not passed here will be lost.

addCustomParams(params), Adds custom params specified by the params objects.
This will be added to the existing params.

removeCustomParams(key), Removed the custom param specified by the key.

saveTypedParams(typedParams), This method is not yet available in the cloud.

Here's an example
```javascript
// Custom parameters definitions in front end UI configuration screen
// Accepts list of objects with the following properties:
// name - used as a key when data is sent from UI
// title - displayed in UI
// defaultValue - optional
// type - optional, can be 'NUMBER', 'STRING' or 'BOOLEAN'. Defaults to 'STRING'
// desc - optional, shown in tooltip in UI
// isRequired - optional, true/false
// isList - optional, true/false, if set this will be treated as list of values
//    or objects by UI
// params - optional, can contain a list of objects.
// 	 If present, then this (parent) is treated as object /
// 	 list of objects by UI, otherwise, it's treated as a
// 	 single / list of single values

const typedParams = [
  {name: 'host', title: 'Host', isRequired: true},
  {name: 'port', title: 'Port', isRequired: true, type: 'NUMBER'},
  {name: 'user', title: 'User', isRequired: true},
  {name: 'password', title: 'Password', isRequired: true},
  // { name: 'list', title: 'List of values', isList:true }
];

poly.saveTypedParams(typedParams);
```


setCustomParamsDoc(html), allows you to set the HTML help file for your params.
This method is not yet available in the cloud,

Here's an example using a markdown file.

```javascript
const fs = require('fs');
const markdown = require('markdown').markdown;

const configurationHelp = './configdoc.md';

const md = fs.readFileSync(configurationHelp);
poly.setCustomParamsDoc(markdown.toHTML(md.toString()));
```


saveCustomData(data), allows you to save data for your node server. This will
overwrite the existing data.

addCustomData(data), allows you to save data for your node server. This will add
to your existing data, as long as the keys are different.

getCustomData(key = null), gives you all of your custom data, or a specific key
if specified.

removeCustomData(key), allows you to delete custom data.

restart(), allows you to self restart the NodeServer.

# Nodes
## A Node has these standard properties

`id` (This is the Nodedef ID)

`polyInterface` (Gives access to the Polyglot interface)

`primary` (Primary address)

`address` (Node address)

`name` (Node name)

`timeAdded` (Time added)

`enabled` (Node is enabled?)

`added` (Node is added to ISY?)

`commands` (List of commands)

`drivers` (List of drivers)

The list of commands in your custom node need to map to a function which is executed when the command command is triggered.

The list of drivers defines the node statuses, the uom, and contains the value.


## A Node has these standard methods

getDriver(driver), to get the driver object.

setDriver(driver, value, report=true, forceReport=false, uom=null), to set a driver to a value (example set ST to 100).

reportDriver(driver, forceReport), to send existing driver value to ISY.

reportDrivers(), To send existing driver values to ISY.

query(), which is called when we get a query request (Override this to fetch live data).

status(), which is called when we get a status request for this node.

delNode(), which will remove the node from Polyglot and the ISY.
