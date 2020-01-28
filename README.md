![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        


# Use SQL To Find How Much Resources An SQL Process Is Using
**Post Date: July 14, 2015**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's some sql logic to help you get the amount of resources any particular user process is taking. It will give you the following information per process.
1. System Name
2. User Name (user responsible for said process)
3. Program Name (program that initiated the process)
4. Time when started
5. Database Name
6. Session Id
7. Row Count (rows affected for this process)
8. CPU Time hh:mm:ss (amount of time the thread spent being processed)
9. Total Scheduled Time hh:mm:ss
10. Elapsed Time hh:mm:ss (duration of process)
11. Memory Usage (in kb)
12. Space Allocated for internal objects
13. Space Allocated for user objects
14. Space unallocated for internal objects
15. Space unallocated for user objects.</p>      


## SQL-Logic
```SQL
/*
Get the page space allocated with each process.
This is an excellent way to determine how many resources are being used in your database processes.
Note:
The counters track the total number of pages that have been allocated or deallocated for
tasks that are already completed in the session. The counters are updated only when a task
ends; they do not reflect running tasks. That 'status' will usually represent 'sleeping' as
the results of this query are from finished processes.
More info can be found here:
https://msdn.microsoft.com/en-us/library/ms187938.aspx
*/
 
use master;
set nocount on
select
'system name' = sdes.host_name
, 'user name' = sdes.login_name
, 'program name' = sdes.program_name
, 'session started' = datename(dw, sdes.login_time) + ' ' + convert(char, sdes.login_time, 9)
, 'database' = db_name(sdes.database_id)
, 'session_id' = sdes.session_id
, 'row count' = sdes.row_count
, 'cpu time hh:mm:ss' = convert(varchar, dateadd(ms, sdes.cpu_time, 0), 114)
, 'total scheduled time hh:mm:ss' = convert(varchar, dateadd(ms, sdes.total_scheduled_time, 0), 114)
, 'elapsed time hh:mm:ss' = convert(varchar, dateadd(ms, sdes.total_elapsed_time, 0), 114)
, 'memory usage in kb' = (sdes.memory_usage * 8)
, 'space allocated - user objects in kb' = (sddssu.user_objects_alloc_page_count * 8)
, 'space allocated - internal objects in kb' = (sddssu.internal_objects_alloc_page_count * 8)
, 'space deallocated - user objects in kb' = (sddssu.user_objects_dealloc_page_count * 8)
, 'space deallocated - internal objects in kb' = (sddssu.internal_objects_dealloc_page_count * 8)
from
sys.dm_db_session_space_usage sddssu inner join sys.dm_exec_sessions sdes on sddssu.session_id = sdes.session_id
where
sdes.database_id > 4
and sdes.cpu_time > 0
--and sdes.is_user_process < 50
and sdes.is_user_process > 0
order by
sdes.login_time desc
```




[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

   
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

