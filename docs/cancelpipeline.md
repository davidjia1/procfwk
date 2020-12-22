# Cancel Pipeline

___
[<< Contents](/procfwk/contents) / [Functions](/procfwk/functions)

___

## Role

To make a recursive cancel request against to the target worker pipeline and [orchestrator](/procfwk/orchestrators) type.

While the pipeline is in a 'cancelling' state the function will wait and return only once a 'cancelled' state has been achieved.

Note; Microsoft side typo's mean 'canceling' may also appear in the logging outputs.

Namespace: __mrpaulandrew.azure.procfwk__.

## Method

POST

## Body Request

```json
{
"tenantId": "123-123-123-123-1234567",
"applicationId": "123-123-123-123-1234567",
"authenticationKey": "Passw0rd123!",
"subscriptionId": "123-123-123-123-1234567",
"resourceGroupName": "ADF.procfwk",
"orchestratorName": "FrameworkFactory",
"orchestratorType": "ADF",
"pipelineName": "Intentional Error",
"runId": "123-123-123-123-1234567"
}
```

## Return

See [Services](/procfwk/services) return classes.

## Example Output

```json
{
"PipelineName": "Wait 1",
"RunId": "c5c2e1e7-bdc8-4eec-b015-cd1aa498b0a4",
"ActualStatus": "Cancelled",
"SimpleStatus": "Complete"
}
```

___