# What do I do if the Validate PK size fail exception occurs when I use SDKs?

This topic describes the problem description, cause, and solution of the Validate PK size fail exception when you use SDKs.

## Problem description

The following exception occurs when you use SDKs to update data:

```
Caused by: [ErrorCode]:OTSInvalidPK, [Message]:Validate PK size fail
```

## Cause

The number of primary keys configured is inconsistent with the actual number of primary keys of the table.

## Solution

The number of primary keys configured must be consistent with the actual number of primary keys of the table.

