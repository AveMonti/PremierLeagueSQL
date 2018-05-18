### PremierLeagueSQL

<img src="/img/img.png" height="438" width="770" />

* Procedura 1
```
create or alter procedure [TopStadion] @City int
as
if @City<=(select count(*) from [Projekt].dbo.[Stadion])
begin
select TOP (@City) [Miasto]
      ,[Stadion]
      ,[Pojemność]
  from [Projekt].dbo.[Stadion]
  order by [Pojemność]
end
else
	begin
		select 'Za duża liczba'
	end

Exec TopStadion 3
go

```

* Procedura 2

```
create or alter procedure [ClubsofSponsor] @Sponsor varchar(100)
as
if @Sponsor  in (select [Sponsor_techniczny] from [Projekt].dbo.[Trenerzy])
begin
select b.[Drużyna],
       a.[Sponsor_techniczny]
  from [Projekt].dbo.[Trenerzy] a
  inner join [Projekt].dbo.[Ranking] b
  on a.[Pozycja]=b.[Pozycja]
  where [Sponsor_techniczny]=@Sponsor
end
else
	begin
		select 'Nie ma takiego Sponsora'
	end

Exec ClubsofSponsor 'Adidas'
go

Exec ClubsofSponsor 'chujwie'
go

```

* Procedura 3
```
create or alter function AvergageOnMatch (@Klub varchar(40))
returns float
as
begin
	declare @average Float;

	Set @average = (select round(Punkty*1.00/Mecze,2) from Projekt.dbo.Ranking where Drużyna = @Klub);

return @average;
end

select Drużyna, dbo.AvergageOnMatch(Drużyna)
from Projekt.dbo.Ranking

```

* Procedura 4
```
create or alter function getPayment (@SumaPodzialu INT,@BonusGol int, @Zawodnik varchar(100),@withPremium bit)
returns float
as
begin
	declare @basicAmount float;
	declare @premiumGoals float;
	declare @numberPlayers int;
	declare @pozycja int;
	declare @Klub varchar(100);


	set @Klub=(select b.Drużyna from Projekt.dbo.Zawodnik a inner join Projekt.dbo.Ranking b on a.ID_Klubu= b.Pozycja where a.Piłkarz=@Zawodnik)
	set @numberPlayers= (select count(*) from Projekt.dbo.Zawodnik a inner join Projekt.dbo.Ranking b on a.ID_Klubu= b.Pozycja where b.Drużyna=@Klub);
	set @pozycja=(select Pozycja from Projekt.dbo.Ranking where Drużyna=@Klub);
	if @pozycja<=5
		set @basicAmount=(0.08*@SumaPodzialu)/@numberPlayers;
	else
		if @pozycja >15
			set @basicAmount=(0.05*@SumaPodzialu)/@numberPlayers;
		else
		 	set @basicAmount=(0.02*@SumaPodzialu)/@numberPlayers;

	if @withPremium=1
	 set @premiumGoals=@BonusGol*(select gole from Projekt.dbo.Strzelcy where Zawodnik=@Zawodnik);
	else
	 set @premiumGoals=0;
return round(@basicAmount,2)+coalesce(@premiumGoals,0);
end
go

 --Premia
select Projekt.dbo.getPayment(10000,1000,'Romelu Lukaku',1);
--brakpremii
select Projekt.dbo.getPayment(10000,1000,'Romelu Lukaku',0);


select top(4)  Zawodnik, Projekt.dbo.getPayment(10000,1000,Zawodnik,1) from [Projekt].[dbo].[Strzelcy];

```

* Widok Indeksowany
```
Use Projekt;
go

create view vChelsea
WITH SCHEMABINDING
AS
select a.Poz, a.Piłkarz,a.Numer
	from dbo.Zawodnik a
	inner join dbo.Ranking b
	on a.ID_Klubu=b.Pozycja
	where b.Drużyna='Chelsea Londyn';
go
CREATE UNIQUE CLUSTERED INDEX IDX_V1
    ON vChelsea (Numer);

select * from vChelsea 

```


* Select 1
```
select b.[Drużyna],
       a.[Sponsor_techniczny]
  from [Projekt].dbo.[Trenerzy] a
  inner join [Projekt].dbo.[Ranking] b
  on a.[Pozycja]=b.[Pozycja]

```
