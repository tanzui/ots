# Table Store ProtocolBuffer Message Definitions {#reference12744 .reference}

The detailed definitions of table\_store.proto and table\_store\_filter.proto are as follows:

## table\_store.proto { .section}

```language-protobuf
package com.alicloud.openservices.tablestore.core.protocol;

message Error {
    required string code = 1;
    optional string message = 2;
}

enum PrimaryKeyType {
    INTEGER = 1;
    STRING = 2;
    BINARY = 3;
}

enum PrimaryKeyOption {
    AUTO_INCREMENT = 1;
}

message PrimaryKeySchema {
    required string name = 1;
    required PrimaryKeyType type = 2;
    optional PrimaryKeyOption option = 3; 
}

message TableOptions {
    optional int32 time_to_live = 1; // It can be dynamically modified
    optional int32 max_versions = 2; // It can be dynamically modified
	optional int64 deviation_cell_version_in_sec = 5; // It can be dynamically modified
}

message TableMeta {
    required string table_name = 1;
    repeated PrimaryKeySchema primary_key = 2;
}

enum RowExistenceExpectation {
    IGNORE = 0;
    EXPECT_EXIST = 1;
    EXPECT_NOT_EXIST = 2;
}

message Condition {
    required RowExistenceExpectation row_existence = 1;
    optional bytes column_condition      = 2;
}

message CapacityUnit {
    optional int32 read = 1;
    optional int32 write = 2;
}

message ReservedThroughputDetails {
    required CapacityUnit capacity_unit = 1; // It indicates the value of the current reserved throughput of the table
    required int64 last_increase_time = 2; // It indicates the last time that the reserved throughput was raised
    optional int64 last_decrease_time = 3; // It indicates the last time that the reserved throughput was lowered
}

message ReservedThroughput {
    required CapacityUnit capacity_unit = 1;
}

message ConsumedCapacity {
    required CapacityUnit capacity_unit = 1;
}

/* #############################################  CreateTable  ############################################# */
/**
 * table_meta is used to store unmodifiable schema attributes in a table. The ReservedThroughput and TableOptions attributes that can be modified are independently stored as the parameters for UpdateTable.
 * message CreateTableRequest {
 *         required TableMeta table_meta = 1;
 *         required ReservedThroughput reserved_throughput = 2;
 *         required TableOptions table_options = 3;
 * }
 */
message CreateTableRequest {
    required TableMeta table_meta = 1;
    required ReservedThroughput reserved_throughput = 2; 
    optional TableOptions table_options = 3;
}

message CreateTableResponse {
}

/* ######################################################################################################### */


/* #############################################  UpdateTable  ############################################# */
message UpdateTableRequest {
    required string table_name = 1;
    optional ReservedThroughput reserved_throughput = 2;
    optional TableOptions table_options = 3;
}

message UpdateTableResponse {
    required ReservedThroughputDetails reserved_throughput_details = 1;
    required TableOptions table_options = 2;
}
/* ######################################################################################################### */

/* #############################################  DescribeTable  ############################################# */
message DescribeTableRequest {
    required string table_name = 1;
}

message DescribeTableResponse {
    required TableMeta table_meta = 1;
    required ReservedThroughputDetails reserved_throughput_details = 2;
    required TableOptions table_options = 3;
    repeated bytes shard_splits = 6;
}
/* ########################################################################################################### */

/* #############################################  ListTable  ############################################# */
message ListTableRequest {
}

/**
 * Only a simple name list is returned currently.
 */
message ListTableResponse {
    repeated string table_names = 1;
}
/* ####################################################################################################### */

/* #############################################  DeleteTable  ############################################# */
message DeleteTableRequest {
    required string table_name = 1;
}

message DeleteTableResponse {
}

/* #############################################  UnloadTable  ############################################# */
message UnloadTableRequest {
    required string table_name = 1;
}

message UnloadTableResponse {

}
/* ########################################################################################################## */

/**
 * The minimum and maximum values of the time stamp are 0 and INT64. MAX, respectively.
 * 1. To query data of a time range, specify start_time and end_time.
 * 2. To query data of a specific time stamp, specify specific_time.
 */
message TimeRange {
    optional int64 start_time = 1;
    optional int64 end_time = 2;
    optional int64 specific_time = 3;
}

/* #############################################  GetRow  ############################################# */

enum ReturnType {
    RT_NONE = 0;
    RT_PK = 1;
}

message ReturnContent {
    optional ReturnType return_type = 1;
} 

/**
 * 1. You can specify the time stamp range or time to read the columns of the specified version.
 * 2. Intra-row breakpoints are not supported currently.
 */
message GetRowRequest {
    required string table_name = 1;
    required bytes primary_key = 2; // encoded as InplaceRowChangeSet, but only has primary key
    repeated string columns_to_get = 3; // If it is not specified, all columns are read
    optional TimeRange time_range = 4;
    optional int32 max_versions = 5;
    optional bytes filter = 7;
    optional string start_column = 8;
    optional string end_column = 9;
    optional bytes token = 10;
}

message GetRowResponse {
    required ConsumedCapacity consumed = 1;
    required bytes row = 2; // encoded as InplaceRowChangeSet
    optional bytes next_token = 3;
}
/* #################################################################################################### */

/* #############################################  UpdateRow  ############################################# */
message UpdateRowRequest {
    required string table_name = 1;
    required bytes row_change = 2;
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}

message UpdateRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}
/* ####################################################################################################### */

/* #############################################  PutRow  ############################################# */
/**
 * You can set a time stamp for each column instead of setting a unified time stamp for the entire row.
 */
message PutRowRequest {
    required string table_name = 1;
    required bytes row = 2; // encoded as InplaceRowChangeSet
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}

message PutRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}
/* #################################################################################################### */

/* #############################################  DeleteRow  ############################################# */
/**
 * Table Store only supports deleting all versions of all columns in a specified row.
 */
message DeleteRowRequest {
    required string table_name = 1;
    required bytes primary_key = 2; // encoded as InplaceRowChangeSet, but only has primary key
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}

message DeleteRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}
/* ####################################################################################################### */

/* #############################################  BatchGetRow  ############################################# */
message TableInBatchGetRowRequest {
    required string table_name = 1;
    repeated bytes primary_key = 2; // encoded as InplaceRowChangeSet, but only has primary key
    repeated bytes token = 3;
    repeated string columns_to_get = 4;  // If it is not specified, all columns are read
    optional TimeRange time_range = 5;
    optional int32 max_versions = 6;
    optional bytes filter = 8;
    optional string start_column = 9;
    optional string end_column = 10;
}

message BatchGetRowRequest {
    repeated TableInBatchGetRowRequest tables = 1;
}

message RowInBatchGetRowResponse {
    required bool is_ok = 1;
    optional Error error = 2;
    optional ConsumedCapacity consumed = 3;
    optional bytes row = 4; // encoded as InplaceRowChangeSet
    optional bytes next_token = 5;
}

message TableInBatchGetRowResponse {
    required string table_name = 1;
    repeated RowInBatchGetRowResponse rows = 2;
}

message BatchGetRowResponse {
    repeated TableInBatchGetRowResponse tables = 1;
}
/* ######################################################################################################### */

/* #############################################  BatchWriteRow  ############################################# */

enum OperationType {
    PUT = 1;
    UPDATE = 2;
    DELETE = 3;
}

message RowInBatchWriteRowRequest {
    required OperationType type = 1;
    required bytes row_change = 2; // encoded as InplaceRowChangeSet
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}

message TableInBatchWriteRowRequest {
    required string table_name = 1;
    repeated RowInBatchWriteRowRequest rows = 2;
}

message BatchWriteRowRequest {
    repeated TableInBatchWriteRowRequest tables = 1;
}

message RowInBatchWriteRowResponse {
    required bool is_ok = 1;
    optional Error error = 2;
    optional ConsumedCapacity consumed = 3;
    optional bytes row = 4; 
}

message TableInBatchWriteRowResponse {
    required string table_name = 1;
    repeated RowInBatchWriteRowResponse rows = 2;
}

message BatchWriteRowResponse {
    repeated TableInBatchWriteRowResponse tables = 1;
}
/* ########################################################################################################### */

/* #############################################  GetRange  ############################################# */
enum Direction {
    FORWARD = 0;
    BACKWARD = 1;
}

message GetRangeRequest {
    required string table_name = 1;
    required Direction direction = 2;
    repeated string columns_to_get = 3;  // If it is not specified, all columns are read
    optional TimeRange time_range = 4;
    optional int32 max_versions = 5;
    optional int32 limit = 6;
    required bytes inclusive_start_primary_key = 7; // encoded as InplaceRowChangeSet, but only has primary key
    required bytes exclusive_end_primary_key = 8; // encoded as InplaceRowChangeSet, but only has primary key
    optional bytes filter = 10;
    optional string start_column = 11;
    optional string end_column = 12;
    optional bytes token = 13;
}

message GetRangeResponse {
    required ConsumedCapacity consumed = 1;
    required bytes rows = 2; // encoded as InplaceRowChangeSet
    optional bytes next_start_primary_key = 3; // If it is null, all data has been read. It is encoded as InplaceRowChangeSet, but only has primary key
    optional bytes next_token = 4;
}

/* ####################  ComputeSplitPointsBySize  #################### */
message ComputeSplitPointsBySizeRequest {
    required string table_name = 1;
    required int64 split_size = 2; // in 100MB
}

message ComputeSplitPointsBySizeResponse {
    required ConsumedCapacity consumed = 1;
    repeated PrimaryKeySchema schema = 2;

    /**
     * Split points between splits, in the increasing order
     *
     * A split is a consecutive range of primary keys,
     * whose data size is about split_size specified in the request.
     * The size could be hard to be precise.
     *
     * A split point is an array of primary-key column w.r.t. table schema,
     * which is never longer than that of table schema.
     * Tailing -inf will be omitted to reduce transmission payloads.
     */
    repeated bytes split_points = 3;

    /**
     * Locations where splits lies in.
     *
     * By the managed nature of TableStore, these locations are no more than hints.
     * If a location is not suitable to be seen, an empty string will be placed.
     */
     message SplitLocation {
         required string location = 1;
         required sint64 repeat = 2;
     }
     repeated SplitLocation locations = 4;
}

```

## table\_store\_filter.proto { .section}

```language-protobuf
package com.alicloud.openservices.tablestore.core.protocol;

enum FilterType {
    FT_SINGLE_COLUMN_VALUE = 1;
    FT_COMPOSITE_COLUMN_VALUE = 2;
    FT_COLUMN_PAGINATION = 3;
}

enum ComparatorType {
    CT_EQUAL = 1;
    CT_NOT_EQUAL = 2;
    CT_GREATER_THAN = 3;
    CT_GREATER_EQUAL = 4;
    CT_LESS_THAN = 5;
    CT_LESS_EQUAL = 6;
}

message SingleColumnValueFilter {
    required ComparatorType comparator = 1;
    required string column_name = 2;
    required bytes column_value = 3;
    required bool filter_if_missing = 4;
    required bool latest_version_only = 5; 
}

enum LogicalOperator {
    LO_NOT = 1;
    LO_AND = 2;
    LO_OR = 3;
}

message CompositeColumnValueFilter {
    required LogicalOperator combinator = 1;
    repeated Filter sub_filters = 2;
}

message ColumnPaginationFilter {
    required int32 offset = 1;
    required int32 limit = 2;
}

message Filter {
    required FilterType type = 1;
    required bytes filter = 2;  // Serialized string of filter of the type
}

```

