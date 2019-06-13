# Queue management {#concept_263747 .concept}

## Obtain a Queue instance {#section_l02_19g_21f .section}

A Queue is an abstract of a one message queue. The Queue corresponds to all messages of an identifier under a TimelineStore. You can call the required operation of TimelineStore to create a Queue instance.

``` {#codeblock_pns_763_z1q}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group_1")
        .build();

// The Queue corresponds to an identifier of a TimelineStore.
TimelineQueue timelineQueue = timelineStore.createTimelineQueue(identifier);
```

The Queue instance manages a message queue that corresponds to an identifier of a TimelineStore. This instance provides some operations, such as Store, StoreAsync, BatchStore, Delete, Update, UpdateAsync, Get, and Scan.

## Store {#section_pbc_peu_she .section}

You can call this operation to synchronously store messages. To use this operation, you can set SequenceId in two ways: auto-increment column and manual setting.

``` {#codeblock_j1p_dr0_bkw}
timelineQueue.store(message);//Auto-increment column
timelineQueue.store(sequenceId, message);//Manual setting
```

## StoreAsync {#section_qlv_2wd_hky .section}

You can call this operation to asynchronously store messages. You can customize callbacks to process successful or failed storage. This operation returns Future<TimelineEntry\>.

``` {#codeblock_x26_2y9_kf6}
TimelineCallback callback = new TimelineCallback() {
    @Override
    public void onCompleted(TimelineIdentifier i, TimelineMessage m, TimelineEntry t) {
        // do something when succeed.
    }

    @Override
    public void onFailed(TimelineIdentifier i, TimelineMessage m, Exception e) {
        // do something when failed.
    }
};

timelineQueue.storeAsync(message, callback);//Generate the SequenceId value by using an auto-increment column.
timelineQueue.storeAsync(sequenceId, message, callback);//Specify the SequenceId value by manual.
```

## BatchStore {#section_cwn_43i_ids .section}

You can call this operation to store multiple messages in the callback and non-callback ways. You can customize callbacks to process successful or failed storage.

``` {#codeblock_uph_bnw_55t}
timelineQueue.batchStore(message);//Auto-increment column
timelineQueue.batchStore(sequenceId, message);//Manual setting

timelineQueue.batchStore(message, callback);//Auto-increment column
timelineQueue.batchStore(sequenceId, message, callback);//Manual setting
```

## Get {#section_q1b_wdn_zne .section}

You can call this operation to read one row based on the SequenceId value. If the message does not exist, no error occurs and the system returns null.

``` {#codeblock_qtd_qst_veo}
timelineQueue.get(sequenceId);
```

## GetLatestTimelineEntry {#section_e1e_oi5_9z0 .section}

You can call this operation to read the latest message. If the message does not exist, no error occurs and the system returns null.

``` {#codeblock_v3h_c95_1mx}
timelineQueue.getLatestTimelineEntry();
```

## GetLatestSequenceId {#section_9g5_qp7_9mr .section}

You can call this operation to obtain the SequenceId value of the latest message. If the message does not exist, no error occurs and the system returns 0.

``` {#codeblock_xt4_vvu_dqq}
timelineQueue.getLatestSequenceId();
```

## Update {#section_f73_vs3_3yb .section}

You can call this operation to synchronously update a message based on the SequenceId value.

``` {#codeblock_3q5_4rw_2kx}
TimelineMessage message = new TimelineMessage().setField("text", "Timeline is fine.");

//update message with new field
message.setField("text", "new value");
timelineQueue.update(sequenceId, message);
```

## UpdateAsync {#section_jhp_a3u_sr6 .section}

You can call this operation to asynchronously update a message based on the SequenceId value. You can customize callbacks to process a successful or failed update. This operation returns Future<TimelineEntry\>.

``` {#codeblock_t1f_fo3_2jo}
TimelineMessage oldMessage = new TimelineMessage().setField("text", "Timeline is fine.") ;
TimelineCallback callback = new TimelineCallback() {
    @Override
    public void onCompleted(TimelineIdentifier i, TimelineMessage m, TimelineEntry t) {
        // do something when succeed.
    }

    @Override
    public void onFailed(TimelineIdentifier i, TimelineMessage m, Exception e) {
        // do something when failed.
    }
};

TimelineMessage newMessage = oldMessage;
newMessage.setField("text", "new value");
timelineQueue.updateAsync(sequenceId, newMessage, callback);
```

## Delete {#section_8zj_bbp_lub .section}

You can call this operation to delete one row based on the SequenceId value.

``` {#codeblock_nl4_r30_wnz}
timelineQueue.delete(sequenceId);
```

## Scan {#section_sgt_74p_zwt .section}

You can call this operation to read messages in one queue in forward or backward order based on the Scan parameter. This operation returns Iterator<TimelineEntry\>. You can iterate all result sets by using the iterator.

``` {#codeblock_qdh_ydo_3qb}
ScanParameter scanParameter = new ScanParameter().scanBackward(Long.MAX_VALUE, 0);

timelineQueue.scan(scanParameter);
```

