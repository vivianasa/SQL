# SQL
## 简易搜索：
### 
      select * from [HFData2].[dbo].[HFData2_ZZ_temp]
      where symbol='ER' and time between '2016-01-15' and '2016-01-22'
      order by time
      
### 选择第一行和倒数第一行
      select top 1 * from [HFData2].[dbo].[HFData2_ZZ] order by time 
      select top 1 * from [HFData2].[dbo].[HFData2_ZZ] order by time desc
      
### 选择其中几列
      select day(time) 日期,count(*) counter from HFData2_ZZ
      where time between '2016-01-14' and '2016-01-22' and symbol='CF'
      group by day(time)
      
## 数据类型  
### 改变数据类型
      select * from [HFData2].[dbo].[HFData2_ZZ]
      where symbol='CF' and convert (varchar(20),time,120) like '2017-12-22%'
      order by time
###  
        ALTER TABLE HF_business_day_ZZ
        ALTER COLUMN date datetime null

### csv数据类型为日期的-->2019/4/12 到sql才可以正常显示为datetime类型
 join时 null->0 select a.id,isnull(b.name,0) from a left join b on a.id=b.id
 
 
 
 
      
## Group By
###
      select min(time) 挂牌日,
        max(time) 摘牌日,
        filename 合约名
        from HFData2_ZZ
        where symbol='SR' and time between '2009-1-2' and '2010-01-31'
        group by filename
        order by filename


###
      set SR811=CONCAT(SR8111,'MM')
      

##  日盘夜盘      
### 区分日盘夜盘：
      日盘： where datepart(hour,time)>=9 and datepart(hour,time)<=14
      夜盘： where datepart(hour,time)<9 or datepart(hour,time)>14
      
        select *
        from SQ_missing_data_1221
        where symbol='al' and expiration_month='201802' and (datepart(hour,time)<=9 or datepart(hour,time)>=14)
        order by time
        
        
### 计算tau：

      select * from [HFData2].[dbo].[ZJ_contract_info] a
        inner join [HFData2].[dbo].[ZJ_business_day] b
        on a.[date]=b.business_day
        inner join [HFData2].[dbo].[ZJ_business_day] c
        on a.contract_end=c.business_day
        order by a.[date]
        
### 不选择其中的行

      select * from [HFData2].[dbo].[HF_rollover_day]
         where futures_market ='ZZ' and symbol = 'ZC' and time between '2019-04-01' and '2019-05-01' 
                    and (type not like 'T+1' and type not like 'T+0' ) 
                    order by  time asc
                    
                    
### 
      SELECT symbol as n, count(symbol) as N
        FROM log_return_fzx_ZZ where time>
        '2015-08-15'and time<='2015-09-12'
        GROUP BY symbol 
        having count(symbol) =(select max(temp.n) from(SELECT symbol, count(symbol) as n FROM 
        log_return_fzx_ZZ where time>
        '2015-08-15'and time<='2015-09-12'
        GROUP BY symbol)temp)
        
### 
      SELECT symbol, avg(Log_return) AS avg1  FROM   log_return_fzx_ZZ where time>
      '2015-06-13' and time<= '2015-06-20' GROUP BY symbol order by avg1  desc


###   isnull 不生效的原因：
      
      IF EXISTS (
        SELECT
          ISNULL(historyval, 0.0)
        FROM
          ce_bf_l_energyh_t_2
        WHERE
          tagname = '123'
      )
      BEGIN
        --如果存在
        SELECT
          ISNULL(historyval, 0.0)
        FROM
          ce_bf_l_energyh_t_2
        WHERE
          tagname = '123'
        END
        ELSE

        BEGIN
          --如果不存在
          SELECT
            0.0
          END



### 
       select t2.symbol,avg1 from((SELECT symbol, avg(Log_return) AS avg1  FROM   log_return_fzx_ZZ where time>
      '2015-06-07' and time<= '2015-06-20' GROUP BY symbol )t1
       JOIN
      (SELECT symbol, count(symbol) N FROM log_return_fzx_ZZ 
         where time>'2015-07-18'and time<='2015-08-15'
         GROUP BY symbol 
         having count(symbol) = (
          select max(temp.n) from
           (
           SELECT symbol, count(symbol) as N FROM log_return_fzx_ZZ 
           where time>'2015-07-18'and time<='2015-08-15'
           GROUP BY symbol) temp
           ))t2
      ON t1.symbol=t2.symbol) order by avg1  desc
      
      
      
 ### 
       SELECT
      *
      FROM
      (SELECT symbol, avg(Log_return) AS avg1  FROM   log_return_fzx_ZZ where time>
      '2015-06-07' and time<= '2015-06-20' GROUP BY symbol )t1
       left JOIN
      (SELECT symbol, count(symbol) N FROM log_return_fzx_ZZ 
         where time>'2015-07-18'and time<='2015-08-15'
         GROUP BY symbol 
         having count(symbol) = (
          select max(temp.n) from
           (
           SELECT symbol, count(symbol) as N FROM log_return_fzx_ZZ 
           where time>'2015-07-18'and time<='2015-08-15'
           GROUP BY symbol) temp
           ))t2
      ON t1.symbol=t2.symbol order by avg1  desc
      
  
### count

         SELECT symbol, count(symbol) as N FROM log_return_fzx_ZZ 
         where time>'2015-08-15'and time<='2015-10-09 '
         GROUP BY symbol
         

 
### 

      select b.[time],isnull(a.symbol,'MA'),isnull(a.Log_return,0) from
      (SELECT *  FROM   log_return_fzx_ZZ where time>
      '2015-01-01' and time<= '2020-07-20' and symbol='MA') a
       right join 
       (SELECT *  FROM   logreturn_fzx_ZZfri where time>
      '2015-01-01' and time<= '2020-07-20')  b
        on a.[time]=b.[time]
        order by a.[time]

### 总行数：
      select count(*) from HF_business_day_ZZ 
      
      
      
      
 
      
      
### 
      SELECT symbol, avg(Log_return) AS avg1  FROM   log_return_fri_fzx_ZZ where time> 
                '2015-03-29 ' and time<= '2015-06-20'GROUP BY symbol having sum(Log_return) !=0 
             order by avg1  desc

      select t2.symbol,avg1 from((SELECT symbol, avg(Log_return) AS avg1  FROM   log_return_fri_fzx_ZZ where time> 
                '2015-03-29 ' and time<= '2015-06-20'GROUP BY symbol having sum(Log_return) !=0 
       )t1
      inner JOIN
      (SELECT symbol FROM log_return_fri_fzx_ZZ 
         where time>'2015-07-18'and time<='2015-08-15'
         GROUP BY symbol 
         having  sum(Log_return) !=0
           )t2
      ON t1.symbol=t2.symbol) order by avg1  desc


###
      select t2.symbol,avg1 from((SELECT symbol, avg(Log_return) AS avg1  FROM   log_return_fri_fzx_ZZ where time> 
      '2015-05-29' and time<= '2015-06-26' GROUP BY symbol having sum(Log_return) !=0 )t1 inner JOIN 
      (SELECT symbol FROM log_return_fri_fzx_ZZ  where time>
       '2015-07-24'  and time<='2015-08-21'  GROUP BY symbol  having sum(Log_return) !=0 ) t2
       ON t1.symbol=t2.symbol) 
       order by avg1  desc


        select  * from [HFData2].[dbo].[log_return_fri_fzx_ZZ]
        where symbol='MA' and time> '2015-07-24'and time<='2020-09-04'
        order by time
      select * 
      into log_return_fzx_DL_ZZ 
      from (select * from [HFData2].[dbo].[log_return_DL_lxy_isnull] union all select * from [HFData2].[dbo].[log_return_fri_fzx_ZZ])



### 创建新表：


      create table log_return_fzx_DL_ZZ 

      (
      time datetime2(7) NOT NULL primary key,
      symbol nvarchar(50) NOT NULL,

      Current_price float NOT NULL,

      Past_price float NOT NULL,

      Log_return float NOT NULL,

      )
      
      
### 合并两张表到一张新表（未创建）为什么加b还是不知道

        select * 
      into log_return_fzx_DL_ZZ1 
      from (select * from [HFData2].[dbo].[log_return_DL_lxy_isnull] union all select * from [HFData2].[dbo].[log_return_fri_fzx_ZZ]) b


### 把T2的表格全部复制给T1（T1之前没有 执行程序时自动创建）


      SELECT * INTO T1 FROM T2
      
### DELETE
      DELETE FROM [log_return_fri_fzx_ALL_delete1]  where symbol='TS'
      
      
      
###  合并两张表 
##### union有的时候可以正常使用 有的时候不行

      select * 
      into HF_rollover_day_DLIN
      from (select * from [HFData2].[dbo].[HF_rollover_day_DL] 
       where time >= '2010-01-01'
       union 
      select * from [HFData2].[dbo].[HF_rollover_day_IN]
      where time >= '2010-01-01'
      ) b
      
      
      
 ### Distinct：剔除重复项
 ##### 用select into选入一张新表

      select * 
      into [BD_fzx_Day_all1]
      from (SELECT DISTINCT date FROM [BD_fzx_Day_all]) b



