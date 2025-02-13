# IRI configuration options

**The IRI configuration options allow you to customize how your node behaves. You can choose to set these options either in the command line (CL flags) or in a .ini configuration file (config file parameters).**

To make it easier to find the options you want to change, we've separated them into the following categories:

* **API:** How the API responds and which hosts can access it
* **Network:** How your node communicates with neighbors
* **IXI:** How your node uses IXI modules
* **Ledger:** What your node does with its ledger
* **Protocol:** What transactions will be accepted by the network, and how they will be propagated to other nodes
* **ZMQ:** How clients can [subscribe to your node's ZMQ events](../how-to-guides/subscribe-to-events-in-an-iri-node.md)
* **Tip selection:** The length and randomness of the weighted random walk during [tip selection](../concepts/tip-selection.md)
* **Tip solidification:** How tip transactions become solid.
* **Proof of work:** How your node does [proof of work](root://dev-essentials/0.1/concepts/minimum-weight-magnitude.md)
* **Local snapshots:** How and when your node does [local snapshots](../concepts/local-snapshot.md)

:::info:
If you've downloaded the IRI, you can also run it with the `--help` flag to see a list of all the configuration options.
:::

:::info:
All boolean flags require a parameter, for example `--remote=true` or `--remote=false`.
:::

## API

Use these settings to customize how the API behaves and which hosts can access it.

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="api-host"></a> `--api-host`|`API_HOST` |Set the host to which the API listens| string | localhost | To accept any host, set this option to 0.0.0.0 or set the `REMOTE` option to `true`|
|<a name="port"></a>`--port, -p` (**required**) |`API_PORT`|Set the port on which the API listens |string |14265 |
|<a name="max-body-length"></a> `--max-body-length` | `MAX_BODY_LENGTH`|Set a maximum number of characters that the body of an API call may contain|number |1,000,000 | If the length of a request body exceeds this number, an error is returned
|<a name="max-find-transactions"></a>`--max-find-transactions` |`MAX_FIND_TRANSACTIONS`|Set a maximum number of transactions that may be returned by the [`findTransactions` endpoint](../references/api-reference.md#findTransactions) |number | 100,000 | If the number of transactions exceeds this number, an error is returned
|<a name="max-requests-list"></a>`--max-requests-list` |`MAX_REQUESTS_LIST`|Set a maximum number of parameters in an API call |number |1,000 | If the number of parameters exceeds this number, an error is returned
|<a name="max-get-trytes"></a>`--max-get-trytes` |`MAX_GET_TRYTES`|Set a maximum number of trytes that may be returned by the [getTrytes endpoint](../references/api-reference.md#getTrytes)  |number |10,000 | If the number of trytes exceeds this number, an error is returned
|<a name="remote"></a> ` --remote `|-|Open the API interface to any host |boolean | false| When set to true, this option is equivalent to setting the `API_HOST` option to 0.0.0.0. You must add a `true` or `false` value after this flag.
|<a name="remote-auth"></a>`--remote-auth` |`REMOTE_AUTH`|Add basic authentication for API calls in the form of username:password  | string| ""|You can use a plain text or a hashed password|
|<a name="remote-limit-api"></a>`--remote-limit-api` |`REMOTE_LIMIT_API`|Ignore requests to certain API endpoints |array of strings |[[addNeighbors](../references/api-reference.md#addNeighbors), [getNeighbors](../references/api-reference.md#getNeighbors), [removeNeighbors](../references/api-reference.md#removeNeighbors), [attachToTangle](../references/api-reference.md#attachToTangle), [interruptAttachToTangle](../references/api-reference.md#interruptAttachToTangle)] | This option allows you to protect your node against spammers that know the IRI node's URL or IP address.
|<a name="remote-trusted-api-hosts"></a>`--remote-trusted-api-hosts` |`REMOTE_TRUSTED_API_HOSTS`|Hosts that may call any API endpoints, including those set in the `REMOTE_LIMIT_API` option |comma-separated list of strings |localhost | You must also set the `REMOTE` option to `true`

## Network

Use these settings to customize how your node communicates with neighbors.

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="auto-tethering-enabled"></a>`--auto-tethering` |`AUTO_TETHERING`|Enable new connections from unknown neighbors |boolean |false| Unknown neighbors are those that are neither defined in the `NEIGHBORS` option nor added through the `addNeighbors` API endpoint. Use the `MAX_NEIGHBORS` option to limit the maximum number of neighbors that can connect to your node.|
|<a name="cache-size"></a>`--cache-size` |`CACHE_SIZE_BYTES`|Set the maximum size of the network cache in bytes |number |150,000 |
|<a name="dns-refresher"></a>`--dns-refresher` |`DNS_REFRESHER`|Reconnect to neighbors that have dynamic IP addresses |boolean |true |
|<a name="dns-resolution"></a>`--dns-resolution` |`DNS_RESOLUTION`|Enable DNS for neighbor peering |boolean |true|
|<a name="max-neighbors"></a>`--max-neighbors` |`MAX_NEIGHBORS`|Set the maximum number of neighbors |number |5 |This option should be set to at least the same number of URLs that you have in the `NEIGHBORS` option|
|<a name="neighbors"></a>`-n`, `--neighbors` |`NEIGHBORS`|Set the URLs and IP addresses of [neighbors](../how-to-guides/find-neighbor-iri-nodes.md) |array of strings |[""] |
|<a name="neighboring-socket-address"></a>`--neighboring-socket-address` |`NEIGHBORING_SOCKET_ADDRESS`|Bind the TCP server socket for the gossip protocol to an address |string |0.0.0.0|The default address binds the TCP server socket to every network interface. Change this option if your node has multiple IP addresses and you only want to bind to a specific one.|
|<a name="neighboring-socket-port"></a>`--neighboring-socket-port` |`NEIGHBORING_SOCKET_PORT`|Bind the TCP server socket for the gossip protocol to a port |string |15600|
|<a name="p-drop-cache"></a>`--p-drop-cache` |`P_DROP_CACHE_ENTRY`|Set the probability of losing recently seen transactions in the network cache | number between 0 and 1|0.02 |
|<a name="p-remove-request"></a>`--p-remove-request` |`P_REMOVE_REQUEST`|Set the probability of the IRI stopping to request a transaction |number between 0 and 1 |0.01 |This number should be close to 0 so that non-existing transaction hashes are eventually removed
|<a name="queue-size"></a>`--queue-size `|`Q_SIZE_NODE`|Set the maximum size of the REPLY, BROADCAST, and RECEIVE network queues | number|1,000|
|<a name="reconnect-attempt-interval-seconds"></a>`--reconnect-attempt-interval-seconds` |`RECONNECT_ATTEMPT_INTERVAL_SECONDS`|Set the number of seconds to wait before trying to reconnect to a disconnected neighbor |number |60|

## IXI

Use these settings to customize how your node uses IXI modules.

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="ixi-dir"></a>  `--ixi-dir` |`IXI_DIR`|Folder where IXI modules should be added for automatic discovery |string |ixi |

## Ledger

Use these settings to customize what your node does with its ledger.

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="db"></a>`--db` |`MAIN_DB`|Name the database that's used to store transactions  | string | rocksdb | Currently, the only supported database is RocksDB|
|<a name="db-cache-size"></a>`--db-cache-size` |`DB_CACHE_SIZE`|Set the maximum size of the database cache in kilobytes | number|100,000 |
|<a name="db-log-path"></a>`--db-log-path` |`DB_LOG_PATH`|Set the path to the file where the database logs are saved | string |mainnet.log|
|<a name="db-path"></a> `--db-path`| `DB_PATH`|Set the path to the folder where the database is saved|string |mainnetdb |
|<a name="rescan"></a> `--rescan`|`RESCAN_DB`|Rescan all transaction metadata (approvees, bundles, and tags) |boolean |false |
|<a name="revalidate"></a>`--revalidate` |`REVALIDATE`|Reload data in the database about confirmed transactions, and transaction metadata | boolean| false|

## Protocol

Use these settings to customize which transactions will be accepted by the network, and how they will be propagated to other nodes.

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="p-drop-transaction"></a>  `--p-drop-transaction`|`P_DROP_TRANSACTION`|Set the probability of losing a received transaction |number between 0 and 1 |0.0 | This option is available only for testing purposes
|<a name="p-propagate-request"></a>`--p-propagate-request` |`P_PROPAGATE_REQUEST`|Set the probability of the IRI requesting a missing transaction from a neighbor | number|0.01 | This number should be low to avoid the IRI requesting non-existing transactions that spam the network
|<a name="p-reply-random"></a>`--p-reply-random` |`P_REPLY_RANDOM_TIP`|Set the probability of the IRI replying to a random transaction request, even if it doesn't have anything to send|number between 0 and 1 | 0.66|
|<a name="p-select-milestone"></a>`--p-select-milestone`| `P_SELECT_MILESTONE_CHILD`|Set the probability of the IRI requesting a milestone transaction from a neighbor|number between 0 and 1 | 0.7|This number should be large because it's essential that the IRI finds milestones in order to consider transactions confirmed
|<a name="p-send-milestone"></a>`--p-send-milestone` |`P_SEND_MILESTONE`|Set the probability of the IRI sending a milestone transaction as a random reply to its neighbors | number between 0 and 1 | 0.02|

## ZMQ

Use these settings to customize how clients can [subscribe to your node's ZMQ events](../how-to-guides/subscribe-to-events-in-an-iri-node.md).

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="zmq-enabled"></a>  `--zmq-enable-ipc` | `ZMQ_ENABLE_IPC`|Enable [zero message queue](../concepts/zero-message-queue.md) subscriptions through IPC at the `ipc://iri` address| boolean|false |Set the `ZMQ_IPC` option to change the default address|
|`--zmq-enable-tcp` | `ZMQ_ENABLE_TCP`|Enable [zero message queue](../concepts/zero-message-queue.md) subscriptions through TCP on port 5556|boolean|false |Set the `ZMQ_PORT` option to change the default port|
|<a name="zmq-ipc"></a>`--zmq-ipc` |`ZMQ_IPC`|Set the address that is used to communicate with ZMQ through IPC| string|  ipc://iri|
|<a name="zmq-port"></a> `--zmq-port`|`ZMQ_PORT`|Set the port that is used to connect to the ZMQ through TCP|string | 5556|
|<a name="zmq-threads"></a> `--zmq-threads`|`ZMQ_THREADS`|Set the maximum number of threads that the ZMQ publisher can use|number | 1|

## Tip selection

Use these settings to customize the length and randomness of the weighted random walk during [tip selection](../concepts/tip-selection.md).

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="alpha"></a>`--alpha`| `ALPHA`|Set the randomness of the tip selection process             |   number between 0 and infinity  |  0.001     | The number 0 is the most random and infinity is the most deterministic. For an in-depth explanation of this option, [see our blog post](https://blog.iota.org/alpha-d176d7601f1c).|
|<a name="max-analyzed-transactions"></a>`--max-analyzed-transactions` |`MAX_ANALYZED_TXS`|Set the maximum number of unconfirmed transactions that may be analyzed during tip selection to find the latest milestone that references a transaction |number |20,000 |
|  <a name="maxdepth"></a>`--max-depth` |`MAX_DEPTH`|Set the maximum number of previous milestones (depth) from where the IRI will start the tip selection |number |15 | This value should be both small enough to allow the weighted random walk to finish in a reasonable amount of time and large enough in include enough new transactions in the [subgraph](../concepts/tip-selection.md#subgraph-selection)
|<a name="tip-selection-timeout-sec"></a>`--tip-selection-timeout-sec` |`TIP_SELECTION_TIMEOUT_SEC`|Set the maximum number of seconds that the IRI can spend to complete tip selection |number | 60|This option stops your node from stalling if tip selection takes too long

## Tip solidification

Use these settings to customize how tip transactions become solid.

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="tip-selection-timeout-sec"></a>`--tip-solidfier-enabled` |`TIP_SOLIDIFIER_ENABLED`|Enable your node to solidify tip transactions that aren't milestones |boolean | false| By default, the IRI actively tries to solidify only milestones. Other tip transactions become solid through the gossip protocol. If you set this option to `true`, your node will use more resources to solidify non-milestones.


## Proof of work

Use these settings to customize how your node does [proof of work](root://dev-essentials/0.1/concepts/minimum-weight-magnitude.md).

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="pow-threads"></a>`--pow-threads`|`POW_THREADS`   | Number of threads to use for proof-of-work calculations |number  | 0  |

## Local snapshot

Use these settings to customize how and when your node does [local snapshots](../concepts/local-snapshot.md).

| **CL flags** |**Configuration file parameters** |  **Description**| **Accepted values** | **Default value**|**Notes** |
| :------------------------ | :--------------- | :--------- | :--------| :------------|:-----|
|<a name="local-snapshots-enabled"></a>`--local-snapshots-enabled`|`LOCAL_SNAPSHOTS_ENABLED`   | Enable local snapshots |boolean  | true  | This parameter must be set to `true` for the IRI to read any other `LOCAL_SNAPSHOTS` parameters|
|<a name="local-snapshots-pruning-enabled"></a>`--local-snapshots-pruning-enabled`|`LOCAL_SNAPSHOTS_PRUNING_ENABLED`  |  Enable your node to delete old transactions from its database  | false | Transactions are deleted if they were confirmed by a milestone with an index that is older than the result of the following calculation: current milestone index - (`LOCAL_SNAPSHOTS_DEPTH` + `LOCAL_SNAPSHOTS_PRUNING_DELAY`).  |
|<a name="local-snapshots-depth"></a>`--local-snapshots-depth`|`LOCAL_SNAPSHOTS_DEPTH`  | Amount of seen milestones to record in the snapshot.meta file | number starting from 100 | 100 |
|<a name="local-snapshots-pruning-delay"></a>`--local-snapshots-pruning-delay`|`LOCAL_SNAPSHOTS_PRUNING_DELAY`  | Amount of milestone transactions to keep in the ledger   | number starting from 10,000  | 40,000 | We recommend that you use the default value for this option, which triggers a local snapshot every 28 days  |
|<a name="local-snapshots-interval-synced"></a>`--local-snapshots-interval-synced`|`LOCAL_SNAPSHOTS_INTERVAL_SYNCED`  | Interval, in milestone transactions, at which snapshot files are created if the ledger is fully synchronized  |number| 10   |
|<a name="local-snapshots-interval-unsynced"></a>`--local-snapshots-interval-unsynced`|`LOCAL_SNAPSHOTS_INTERVAL_UNSYNCED`   | Interval, in milestone transactions, at which snapshot files are created if the ledger is not fully synchronized  |number| 1,000  | This value is higher than the `LOCAL_SNAPSHOTS_INTERVAL_SYNCED` configuration option to allow the IRI to focus its resources on synchronizing with its neighbor IRI nodes|
|<a name="local-snapshots-base-path"></a>`--local-snapshots-base-path`|`LOCAL_SNAPSHOTS_BASE_PATH`  |  Path to the snapshot file, without the file extension. |  string |  mainnet   | Prepends the `.snapshot.meta` and `.snapshot.state` files with the value of this parameter. For the default value, the files are named `mainnet.snapshot.meta` and `mainnet.snapshot.state`. You can specify a directory for the files to be added to by doing the following: `<directory name>/<file name>`, which results in `folderpath/filename.snapshot.meta`. |
