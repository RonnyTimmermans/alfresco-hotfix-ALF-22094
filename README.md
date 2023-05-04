# alfresco-hotfix-ALF-22094

This project builds an amp that creates a dummy Solr transaction every x hours, to counteract the problem of growing access-logs caused by https://issues.alfresco.com/jira/browse/ALF-22094.

Solr tracks (traces) transactions in the database. By default, Solr does so every 15 seconds. Solr will check in 1-hour intervals using tracking requests until it finds the last transaction. So for every hour between now and the time of the last transaction, an extra request will be sent.

So for example, if I upload a document and no one touches the Alfresco for the next 5 hours, solr will send 5 tracking requests every 15 seconds. 1 for every hour between now and the last transaction (uploading the document). For each of those tracking requests, a line is written in the access logs. So that log is going to get very large very quickly.

The hotfix provided below is going to make an automatic task (scheduled job in Alfresco) create a dummy transaction every hour. It does that by simply changing the description of a dummy document every hour. That way, there will never be a time when there are no new transactions in Alfresco for hours or days. Solr will only send a request once every 15 seconds.
