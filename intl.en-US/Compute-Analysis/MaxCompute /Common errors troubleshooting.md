# Common errors troubleshooting {#concept_59871_zh .concept}

-   Symptoms: FAILED: ODPS-0010000:System internal error - fuxi job failed, WorkerPackageNotExist

    Resolution: Set set odps.task.major.version=unstructured\_data.

-   Symptoms: FAILED: ODPS-0010000:System internal error - std::exception:Message: a timeout was reached

    Resolution: The Table Store endpoint is incorrect, which makes MaxCompute unaccessible. Enter the correct endpoint.

-   Symptoms: logview invalid end\_point

    Resolution: The logview URL is returned during the execution. If an error is returned when a browser is used to access the address, it may be due to an incorrect configuration. Check MaxCompute configuration.


If the problem persists, contact the [after-sales technical support](https://selfservice.console.aliyun.com/ticket/createIndex).

