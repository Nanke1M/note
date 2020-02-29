## 示例
    CREATE TABLE hive_hbase_emp_table( 
    empno int, 
    ename string, 
    job string, 
    mgr int, 
    hiredate string, 
    sal double, 
    comm double, 
    deptno int) 
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
    WITH SERDEPROPERTIES ("hbase.columns.mapping" =":key,info:ename,info:job,info:mgr,info:hiredate,info:sal,info:co mm,info:deptno") 
    TBLPROPERTIES ("hbase.table.name" = "hbase_emp_table"); 


    CREATE EXTERNAL TABLE hiveweibo(
    id string,
    name string,
    url string,
    focus int)
    STORED BY 
    'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,info:name,info:url,info:focus") 
    TBLPROPERTIES ("hbase.table.name" = "weibo");