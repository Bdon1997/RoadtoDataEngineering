# RoadtoDataEngineering
SQL learning database development

USE [Imports]
GO
/****** Object:  StoredProcedure [dbo].[Ticket_Transfer]    Script Date: 08/10/2022 15:55:00 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER procedure [dbo].[Ticket_Transfer] 
as

with CTE as 
    (select tickets_staging.Customer_Name, Tickets_Staging.TicketNumber,Tickets_Staging.Date_bought,Tickets_Staging.Date_expires,
            ROW_NUMBER() over(partition by tickets_staging.Customer_Name, Tickets_Staging.TicketNumber,Tickets_Staging.Date_bought,Tickets_Staging.Date_expires order by tickets_staging.Customer_Name desc) rn
     from Tickets_Staging)

delete CTE where rn > 1;

create table Temp_table
		(id INTEGER,
		 Customer_Name varchar(100),
		 TicketNumber  INTEGER,
		 Date_bought   varchar(100),
		 Date_expires  varchar(100),
		 DateImported  DATETIME2)

insert into Temp_table
select *
from Imports.dbo.Tickets_Staging

alter table Temp_table
alter column Date_bought  Datetime2 

alter table Temp_table
alter column Date_expires Datetime2;

insert into dbo.Tickets_Staging
select *
from Temp_Table

drop table Temp_table;