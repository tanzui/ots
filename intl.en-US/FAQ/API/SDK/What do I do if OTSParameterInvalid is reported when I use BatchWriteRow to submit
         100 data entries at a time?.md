# What do I do if OTSParameterInvalid is reported when I use BatchWriteRow to submit 100 data entries at a time?

## Problem description

A similar output is displayed when I use BatchWriteRow to submit 100 data entries at a time:

```
ErrorCode: OTSParameterInvalid, ErrorMessage: The input parameter is invalid.
```

## Cause

The possible cause is that one batch operation cannot be repeatedly performed on the same row. If the operation is repeatedly performed on the same row, an error occurs.

## Solution

Change 100 to 1 to submit one data entry each time. Keep other code unchanged.

