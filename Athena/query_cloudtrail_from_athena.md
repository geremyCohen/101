# Query CloudTrail data from Athena

After creating the table in Athena, we can now query the data using a SQL-like syntax.

We can pull the five earliest CloudTrail events:

```
select * from cloudtrail_logs
order by eventtime asc
limit 5;
```

How about who has logged in to the web console?

```
select useridentity.username, sourceipaddress, eventtime, additionaleventdata
from cloudtrail_logs
where eventname = 'ConsoleLogin'
and eventtime >= '2019-07-30T00:00:00Z'
and eventtime < '2029-08-18T00:00:00Z';
```

Let's narrow down the scope, and just see events that have to do with people starting EC2 instances:

```
select *
from cloudtrail_logs
where eventname = 'RunInstances'
limit 5
```

Similary, with S3:

```
select useridentity.username, sourceipaddress, eventtime, additionaleventdata
from cloudtrail_logs
where eventname = 'ListBuckets'
```

Independent of service, what are the top 10 error messages?  Lets see:

```
select count (*) as TotalEvents, eventname, errorcode, errormessage
from cloudtrail_logs
where errorcode is not null
and eventtime >= '2017-01-01T00:00:00Z'
group by eventname, errorcode, errormessage
order by TotalEvents desc
limit 10;
```

Similarly, the top 10 IAM events, grouped by user and event:

```
select count (*) as TotalEvents, useridentity.username, eventname
from cloudtrail_logs
where eventtime >= '2017-01-01T00:00:00Z'
and useridentity.type = 'IAMUser'
group by useridentity.username, eventname
order by TotalEvents desc;
```
