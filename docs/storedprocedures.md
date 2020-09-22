# Stored Procedures

___
[<< Contents](/procfwk/contents) / [Database](/procfwk/database)

___

The following stored procedures are ordered by database [schema](/procfwk/schemas) then name.

## Schema: procfwk

___

### CheckForBlockedPipelines

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int

__Role:__ Used within parent [pipeline](/procfwk/pipelines) as part of the sequential foreach activity this procedure establishes if any worker pipelines in the next execution stage are blocked. Then depending on the configured [failure handing](/procfwk/failurehandling) updates the current execution [table](/procfwk/tables) before proceeding.

___

### CheckForEmailAlerts

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@PipelineId|int

__Role:__ Assuming [email alerting](/procfwk/eamilalerting) is enabled this procedure inspects the metadata [tables](/procfwk/tables) and returns a simple true or false (bit value) depending on what alerts are required for a given worker [pipeline](/procfwk/pipelines) Id. This checks is used within the infant pipeline before any effort is spent constructing email content to be sent.

___

### CheckMetadataIntegrity

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@DebugMode|bit

__Role:__ Called early on in the parent [pipeline](/procfwk/pipelines) this procedure serves two purposes. Firstly, to perform a series on basic checks against the database metadata ensuring key conditions are met before Data Factory starts a new execution. See [metadata integrity checks](/procfwk/metadataintegritychecks) for more details. 

Secondly, in the event of an external platform failure where the framework is left in an unexpected state. This procedure queries the current execution [table](/procfwk/tables) and returns values to Data Factory so a [clean up](/procfwk/prevruncleanup) routine can take place as part of the parent [pipeline](/procfwk/pipelines) execution.

___

### CreateNewExecution

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@CallingDataFactoryName|nvarchar

__Role:__ Once the parent [pipeline](/procfwk/pipelines) has completed all pre-execution operations this procedure is used to set a new local execution Id (GUID) value and update the current execution table. For runtime perform an index re-build is also done by this procedure.

___

### ExecutePrecursorProcedure

__Schema:__ procfwk

__Role:__ See [Execution Precursor](/procfwk/executionprecursor) for details on this feature.

___

### ExecutionWrapper

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@CallingDataFactory|nvarchar

__Role:__ This procedure establishes what the framework should do with the current execution table when the parent pipeline is triggered. Depending on the configured [properties](/procfwk/properties) this will then create a new execution run or restart the previous run if a failure has occurred.

___

### GetEmailAlertParts

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@PipelineId|int

__Role:__ When an [email alert](/procfwk/emailalerting) is going to be sent by the framework this procedure gathers up all required metadata parts for the [send email](/procfwk/sendemail) function. This includes the recipient, subject and body. The returning select statement means exactly the format required by the function allowing the infant pipeline to pass through the content from the lookup activity unchanged.

___

### GetPipelineParameters

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@PipelineId|int

__Role:__ Within the child pipeline foreach activity this procedure queries the metadata database for any parameters required by the given worker pipeline Id. What's returned by the stored procedure is a JSON safe string that can be injected into the [execute pipeline](/procfwk/executepipeline) function along with the other worker [pipeline](/procfwk/pipelines) details.

___

### GetPipelinesInStage

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int

__Role:__ Called from the child [pipeline](/procfwk/pipeline) this procedure returns a simple list of all worker pipelines to be executed within a given [execution stage](/procfwk/executionstage). Filtering ensures only worker pipelines that haven't already completed successfully or workers that aren't blocked are returned.

___

### GetPropertyValue

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@PropertyName|varchar

__Role:__ This procedure is used by [Data Factory](/procfwk/datafactory) through the framework [pipelines](/procfwk/pipelines) to return a [property](/procfwk/properties) value from a provided name. This is done so Data Factory can use the SELECT output value directly, rather than this being an actual OUTPUT of the procedure.

___

### GetStages

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier

__Role:__ Returns a distinct list of all enabled [execution stages](/procfwk/executionstages) from the metadata to be used in the parent pipeline squential iterations.

___

### GetWorkerAuthDetails

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int

__Role:__ For a given worker pipeline during an execution run of the processing framework, return the following:

* Tenant Id
* Subscription Id
* Service Principal Name (depending on configured [SPN Handling](/procfwk/spnhandling))
* Service Principal Secret (depending on configured [SPN Handling](/procfwk/spnhandling))

___

### GetWorkerPipelineDetails

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int

__Role:__ For a given worker pipeline during an execution run of the processing framework, return the following:

* Resource Group Name
* Data Factory Name
* Worker Pipeline Name

___

### ResetExecution

__Schema:__ procfwk

__Role:__ In the event of a processing [framework restart](/procfwk/frameworkrestart) this procedure archives off any unwanted records from the current execution table and updates the pipeline status attribute for any workers than didn't complete successfully during the previous execution.

___

### SetErrorLogDetails

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@LocalExecutionId|uniqueidentifier
|@JsonErrorDetails|varchar

__Role:__ For a failed worker pipeline the error message details will be passed to this procedure in there raw JSON form. Then parsed and inserted into the database error log table. See [error details](/procfwk/errordetails) for more information on this feature.

___

### SetExecutionBlockDependants

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@PipelineId|int

__Role:__ When using the [dependency chain](/procfwk/dependencychains) feature for [failure handling](/procfwk/failurehandling) this procedure queries the metadata and applies a 'blocked' status to any downstream (dependant) worker pipelines in the current execution table.

___

### SetLogActivityFailed

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int
|@CallingActivity|varchar

__Role:__ In the event of a wider Azure platform failure where a [pipeline](/procfwk/pipeline) activity failures for unexpected reasons this procedure attempts to update and leave the current execution table with an informational status. This status will typically be the activity that has resulted in the failure outcome. I common use for this procedure is when hitting external resources like the Azure Functions activities within the infant pipeline.

___

### SetLogPipelineCancelled

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int
|@CleanUpRun|bit

__Role:__ Updates the current execution table setting the pipeline status attribute to cancelled for the provided worker Id. Also blocks downstream pipelines depending on the configured [failure handling](/procfwk/failurehandling) behaviour.

___

### SetLogPipelineChecking

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int

__Role:__ During the parent pipeline clean up process. Updates the current execution table setting the pipeline status attribute to checking for the provided worker Id.

___

### SetLogPipelineFailed

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int
|@RunId|uniqueidentifier

__Role:__ Updates the current execution table setting the pipeline status attribute to failed for the provided worker Id. Also blocks downstream pipelines depending on the configured [failure handling](/procfwk/failurehandling) behaviour.

___

### SetLogPipelineLastStatusCheck

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int

__Role:__ During the infant [pipeline](/procfwk/pipelines) Until activity upon each iteration this procedure updates the current execution table with the current date/time in the last check attribute. This offers some visability on how many wait iterations have occurred for a given worker pipeline execution.

___

### SetLogPipelineRunId

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int
|@RunId|uniqueidentifier

__Role:__ Once a given worker pipeline is in progress the Data Factory run Id will be returned from the Azure Function and added to the current execution table using this stored procedure.

___

### SetLogPipelineRunning

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int

__Role:__ Updates the current execution table setting the pipeline status attribute to running for the provided worker Id.

___

### SetLogPipelineSuccess

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int

__Role:__ Updates the current execution table setting the pipeline status attribute to success for the provided worker Id.

___

### SetLogPipelineUnknown

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int
|@PipelineId|int
|@CleanUpRun|bit

__Role:__ Updates the current execution table setting the pipeline status attribute to unknown for the provided worker Id. Also blocks downstream pipelines depending on the configured [failure handling](/procfwk/failurehandling) behaviour.

___

### SetLogStagePreparing

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@ExecutionId|uniqueidentifier
|@StageId|int

__Role:__ Updates the current execution table setting the pipeline status attribute to preparing for all worker pipelines in the provided [execution stage](/procfwk/executionstage) Id.

___

### UpdateExecutionLog

__Schema:__ procfwk

|Parameter Name|Data Type|
|---|:---:|
|@PerformErrorCheck|bit

__Role:__ Called at the end of the parent pipeline as the last thing the processing framework does in the metadata database this procedure validates the contents of the current execution table. If all workers were successful the data will be archived off to the execution log table. Otherwise and exception will be raised.

___

## Schema: procfwkHelpers

___

### AddPipelineDependant

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@PipelineName|nvarchar
|@DependantPipelineName|nvarchar

__Role:__ Applies a relationship between an upstream and downstream worker pipeline while conforming to metadata integrity constraints.

___

### AddPipelineViaPowerShell

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@ResourceGroup|nvarchar
|@DataFactoryName|nvarchar
|@PipelineName|nvarchar

__Role:__ Is used by PowerShell when populating the metadata database with a set of worker pipelines from an existing Data Factory instance. For more details on this feature see how to [Apply ProcFwk To An Existing Data Factory](/procfwk/applytoexistingadfs).

___

### AddProperty

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@PropertyName|varchar
|@PropertyValue|nvarchar
|@Description|nvarchar

__Role:__ Performs an upsert to the database [properties](/procfwk/properties) when a new or existing processing framework property is provided. Internally, a merge statement applies the change to the table.

___

### AddRecipientPipelineAlerts

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@RecipientName|varchar
|@PipelineName|nvarchar
|@AlertForStatus|nvarchar

__Role:__ Assuming the reciptient already exists in the metadata this procedure adds the required link for a given worker pipeline and the requested status values.

The 'AlertForStatus' parameter can be a comma separated list if multiple status values are required.

___

### AddServicePrincipal

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@DataFactory|nvarchar
|@PrincipalId|nvarchar
|@PrincipalSecret|nvarchar
|@SpecificPipelineName|nvarchar
|@PrincipalName|nvarchar

__Role:__ If the database [property](/procfwk/properties) for storing service principals is set to use insert them into the database, this procedure encrypts the parameter values provided and adds them to the database while conforming to metadata integrity constraints.

___

### AddServicePrincipalUrls

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@DataFactory|nvarchar
|@PrincipalIdUrl|nvarchar
|@PrincipalSecretUrl|nvarchar
|@SpecificPipelineName|nvarchar
|@PrincipalName|nvarchar

__Role:__ If the database [property](/procfwk/properties) for storing service principals is set to use Azure Key Vault, this procedure attempts to validate the parameter values provided and adds them to the database while conforming to metadata integrity constraints.
___

### AddServicePrincipalWrapper

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@DataFactory|nvarchar
|@PrincipalIdValue|nvarchar
|@PrincipalSecretValue|nvarchar
|@SpecificPipelineName|nvarchar
|@PrincipalName|nvarchar

__Role:__ Depending on the configuration of the [SPN handling](/procfwk/spnhandling) this procedure passes off parameter values to one of the following stored procedures:
* AddServicePrincipal
* AddServicePrincipalUrls

___

### CheckStageAndPiplineIntegrity

__Schema:__ procfwkHelpers

__Role:__ This procedures uses the optional attribute 'LogicalPredecessorId' within the pipelines table to review and suggest updates to the structure of the worker pipeline metadata considering execution stages and upstream/downstream dependants. The output of the procedure is purely for information.

___

### DeleteMetadataWithIntegrity

__Schema:__ procfwkHelpers

__Role:__ Performs an ordered delete of all metadata from the database while conforming to metadata integrity rules.

___

### DeleteMetadataWithoutIntegrity

__Schema:__ procfwkHelpers

__Role:__ Performs a blanket delete of all database contents for tables in the procfwk [schemas](/procfwk/schemas).

___

### DeleteRecipientAlerts

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@EmailAddress|nvarchar
|@SoftDeleteOnly|bit

__Role:__ Removes or disables a given email recipient from the metadata tables while conforming to metadata integrity rules.

___

### DeleteServicePrincipal

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@DataFactory|nvarchar
|@PrincipalIdValue|nvarchar
|@SpecificPipelineName|nvarchar

__Role:__ Removes a given service principal from the metadata tables while conforming to metadata integrity rules.

___

### GetExecutionDetails

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@LocalExecutionId|uniqueidentifier

__Role:__ Used as runtime helper offered more details about the current execution run with additional table joins and summary information.

___

### GetServicePrincipal

__Schema:__ procfwkHelpers

|Parameter Name|Data Type|
|---|:---:|
|@DataFactory|nvarchar
|@PipelineName|nvarchar

__Role:__ Depending on the configured [properties](/procfwk/properties) this procedure queries the service principal table and returns credentials that [Data Factory](/procfwk/datafactory) can use when executing a worker pipeline. See [worker SPN storage](/procfwk/spnhandling) for more details.

___

### SetDefaultAlertOutcomes

__Schema:__ procfwkHelpers

__Role:__ Adds the default email alerting outcome values to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultDataFactorys

__Schema:__ procfwkHelpers

__Role:__ Adds a set of default Data Factory values to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___


### SetDefaultPipelineDependants

__Schema:__ procfwkHelpers

__Role:__ Adds a simple set of default pipeline dependencies to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultPipelineParameters

__Schema:__ procfwkHelpers

__Role:__ Adds a set of default pipeline parameter values to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultPipelines

__Schema:__ procfwkHelpers

__Role:__ Adds a set of default pipeline values to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultProperties

__Schema:__ procfwkHelpers

__Role:__ Adds all default framework [property](/procfwk/properties) values to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultRecipientPipelineAlerts

__Schema:__ procfwkHelpers

__Role:__ Adds a set of default alerting relationship values to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultRecipients

__Schema:__ procfwkHelpers

__Role:__ Adds several default recipient values to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultStages

__Schema:__ procfwkHelpers

__Role:__ Adds a set of default execution stage values to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultSubscription

__Schema:__ procfwkHelpers

__Role:__ Adds a default subscription value to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

### SetDefaultTenant

__Schema:__ procfwkHelpers

__Role:__ Adds a default tenant value to the metadata [table](/procfwk/table) used as part of the processing framework development environment.

___

## Schema: procfwkTesting

___

### Add300WorkerPipelines

__Schema:__ procfwkTesting

__Role:__ Applies a set of 300 worker pipelines with parameters and SPN details to the metadata database as part of the setup for an integration run stress test.

___

### CleanUpMetadata

__Schema:__ procfwkTesting

__Role:__ Used as a wrapper for the helper stored procedures __DeleteMetadataWith***__ to add a empty all metadata from the database. Typically called as part of integration test one time tear downs. See [testing](/procfwk/testing) for more details on this feature.

___

### GetRunIdWhenAvailable

__Schema:__ procfwkTesting

|Parameter Name|Data Type|
|---|:---:|
|@PipelineName|nvarchar

__Role:__ During the integration tests to cancel a running worker pipeline this procedure queries the current execution table waiting for a Run Id value to become available. Once found, the wait iteration breaks and provides the first value found.

___

### ResetMetadata

__Schema:__ procfwkTesting

__Role:__ Used as a wrapper for the helper stored procedures __SetDefault***__ to add a complete set of default metadata to the database. Typically called as part integration test one time setups. See [testing](/procfwk/testing) for more details on this feature.

___