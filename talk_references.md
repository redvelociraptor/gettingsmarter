#PLA1335 References

##Essential Splunk SmartStore Documentation
 * [SmartStore Architecture Overview](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/SmartStorearchitecture)
 * [The SmartStore Cache Manager](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/SmartStorecachemanager)
 * [How Indexing Works in SmartStore](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/SmartStoreindexing)
 * [How Search Works in SmartStore](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/SmartStoresearching)
 * [Indexer Cluster Operations and SmartStore](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/IndexerclusteroperationsandSmartStore)

###Splunk SmartStore Restrictions
The restrictions on SmartStore use as of July 2023 (refer to Splunk docs [About SmartStore](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/AboutSmartStore#Current_restrictions_on_SmartStore_use) for more details): 

 * Replication factor and search factor must be equal (for example, 3/3 or 2/2) if using indexer clustering
 * Each index's home path and cold path must point to the same partition
 * Some index.conf settings are restricted or incompatible, refer to [Settings in indexes.conf that are incompatible with SmartStore or otherwise restricted](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/ConfigureSmartStore#Settings_in_indexes.conf_that_are_incompatible_with_SmartStore_or_otherwise_restricted)
 * Converting to SmartStore has **no roll-back capability**.
 * In a multisite index cluster, any SmartStore enabled index must have search affinity disabled if report acceleration or data model acceleration is used. E.g. Set all search heads to site0.

###Getting Data In
Aplura has a handy reference for GDI available: [Data Onboarding Cheat Sheet](https://www.aplura.com/assets/pdf/onboarding_cheatsheet.pdf)

####Essential Best Practice for HEC: 
If you use the HEC indexer acknowledgement feature, ensure the following:

 * useACK is enabled at your indexers for the HEC endpoint you are using. In Splunk Cloud Platform, useACK is **only** enabled for the Firehose endpoint, as an example.
 * Any loadbalancer in front of your indexers has sticky sessions enabled.

Failure to have the indexers and load balancers properly configured will cause:

 * Duplicated data. Since the forwarder doesn't receive an ack from the indexer, it resends the data.
 * In an environment where healthchecks are used to automate server reprovisions or to quarantine servers temporarily for recovery, indexing may stop. As the ack queue fills on an indexer it will fail to respond to health check requests from the environment's load balancer (e.g. ELB in AWS) or other health monitoring system. In the worst case scenario, if the misconfiguration is not resolved, the entire index cluster may stop indexing data. Rolling restarts temporarily resolve the issue until the ack queues fill again.

Refer to [About HTTP Event Collector Indexer Acknowledgment](https://docs.splunk.com/Documentation/Splunk/latest/Data/AboutHECIDXAck) in Splunk docs to learn how it works and differs from forwarder useACK and complete details to implement it.


Source here: https://github.com/redvelociraptor/gettingsmarter/
[![CC BY-NC-ND 4.0][cc-by-nc-nd-image]][cc-by-nc-nd]
[cc-by-nc-nd]: http://creativecommons.org/licenses/by-nc-nd/4.0/
[cc-by-nc-nd-image]: https://i.creativecommons.org/l/by-nc-nd/4.0/88x31.png
[cc-by-nc-nd-shield]: https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey.svg