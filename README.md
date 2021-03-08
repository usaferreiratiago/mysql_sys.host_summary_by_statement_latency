# mysql_sys.host_summary_by_statement_latency

SELECT 
    IF((`performance_schema`.`events_statements_summary_by_host_by_event_name`.`HOST` IS NULL),
        'background',
        `performance_schema`.`events_statements_summary_by_host_by_event_name`.`HOST`) AS `host`,
    SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`COUNT_STAR`) AS `total`,
    FORMAT_PICO_TIME(SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_TIMER_WAIT`)) AS `total_latency`,
    FORMAT_PICO_TIME(MAX(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`MAX_TIMER_WAIT`)) AS `max_latency`,
    FORMAT_PICO_TIME(SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_LOCK_TIME`)) AS `lock_latency`,
    SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_ROWS_SENT`) AS `rows_sent`,
    SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_ROWS_EXAMINED`) AS `rows_examined`,
    SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_ROWS_AFFECTED`) AS `rows_affected`,
    (SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_NO_INDEX_USED`) + SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_NO_GOOD_INDEX_USED`)) AS `full_scans`
FROM
    `performance_schema`.`events_statements_summary_by_host_by_event_name`
GROUP BY IF((`performance_schema`.`events_statements_summary_by_host_by_event_name`.`HOST` IS NULL),
    'background',
    `performance_schema`.`events_statements_summary_by_host_by_event_name`.`HOST`)
ORDER BY SUM(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_TIMER_WAIT`) DESC
