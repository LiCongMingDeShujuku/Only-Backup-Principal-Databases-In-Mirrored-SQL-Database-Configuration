![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# POST TITLE
#### SECONDARY TITLE
**TIME STAMP**

![#](images/##############?raw=true "#")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
以下逻辑（logic）可以将它自己放入Jo作业中。下面是它会做的事情：
1. 为具有ONLINE状态的所有数据库创建备份。
2. 为所有设置为PRINCIPAL而非MIRRORS的数据库创建备份。 这将确保数据库将正确备份，因为镜像将使作业出错。 因此，这可以部署在任何SQL实例上（镜像或不镜像），并且只备份符合条件的数据库。
3. 检查是否启用了某些功能，例如“show advanced options”，“xp_cmdshell”和“backup compression default”。 如果之前没有设置它们，它们将立即启用。
4. 为备份文件名添加人类可读的时间戳。
5. 检查数据库是否正在进行当前备份，记录或还原操作，如果是， 将忽略数据库，以免引起错误或冲突。



## English
The following logic can be placed into a Job by it’s self. Here’s what it does: 
1. Creates a backup for all databases with an ONLINE state.
2. Creates a backup for all databases that are set as PRINCIPAL not MIRRORS. This will ensure that the databases will backup properly as Mirrored will error the Job. As a result this can be deployed on any SQL instance (mirrored or not), and will backup only databases that qualify.
3. Checks to see if certain features are enabled such as ‘show advanced options’, ‘xp_cmdshell’, and ‘backup compression default’. They will be immediately enabled if they haven’t been formerly set.
4. Adds a human-readable timestamp to the backup file name.
5. Checks to see if Databases are undergoing a current backup, log, or restore operation and if so; will ignore the database as not to cause an error or conflict.


---
## Logic
```SQL
use master;
set nocount on
 
declare @backup_all_databases   varchar(max)
declare @get_time       varchar(25)
declare @get_day        varchar(25)
declare @get_date       varchar(25)
declare @get_month      varchar(25)
declare @get_year       varchar(25)
declare @get_timestamp  varchar(255)
set     @get_day        = (select datename(dw, getdate()))
set     @get_date       = (select datename(dd, getdate()))
set     @get_month      = (select datename(mm, getdate()))
set     @get_year       = (select datename(yy, getdate()))
set     @get_time       = (select replace(replace(replace(replace(convert(char(20), getdate(), 22), '/', '-'), 'AM', 'am'), 'PM', 'pm'), ':', '-'))
set     @get_timestamp          = (select @get_time + ' ' + @get_month + ' ' + @get_day + ' ' + @get_date + ' ' + @get_year + ' Full Database Bu ')
set     @backup_all_databases   =   ''
select  @backup_all_databases   =   @backup_all_databases +
'
    if exists 
    (
    select 1 
    command from master.sys.dm_exec_requests where
    command in (''backup database'', ''backup log'', ''restore database'') 
    and db_name(database_id) = ''' + upper(name) + '''
    )
        begin
            print ''Database: [' + upper(name) + '] Has a backup or restore operation currently running.  Backup will be skipped.''
        end
        else
            backup database [' + upper(name) + '] to disk = ''E:\SQLBACKUPS\' + @get_timestamp + upper(name) + '.bak'' with format;
' + char(10)
from
    sys.databases sd join sys.database_mirroring sdm on sd.database_id = sdm.database_id
where
    name    not in ('tempdb')
    and     state_desc = 'online'
    and     sd.source_database_id   is null
    and     sdm.mirroring_role_desc is null
    or      sdm.mirroring_role_desc != 'mirror'
order by
    name asc
 
declare
    @sao    int = (select cast([value] as int) from master.sys.configurations where [name] = 'show advanced options')
,   @bcd    int = (select cast([value] as int) from master.sys.configurations where [name] = 'backup compression default')
,   @xpc    int = (select cast([value] as int) from master.sys.configurations where [name] = 'xp_cmdshell')
 
if  @sao = 0    begin exec  master..sp_configure 'show advanced options', 1         reconfigure end
if  @bcd = 0    begin exec  master..sp_configure 'backup compression default', 1    reconfigure end
if  @xpc = 0    begin exec  master..sp_configure 'xp_cmdshell', 1                   reconfigure end
exec    (@backup_all_databases)


```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

