<pre>
select t.column_name, comments
FROM all_tab_columns t, ALL_COL_COMMENTS c
where t.table_name like 'KGT_KSIEGOWANIA'
  and t.column_name = c.column_name
  and t.table_name = c.table_name
  order by column_id
</pre>
