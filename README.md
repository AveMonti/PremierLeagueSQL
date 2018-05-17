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


* Select 1
```
select b.[Drużyna],
       a.[Sponsor_techniczny]
  from [Projekt].dbo.[Trenerzy] a
  inner join [Projekt].dbo.[Ranking] b
  on a.[Pozycja]=b.[Pozycja]

```
