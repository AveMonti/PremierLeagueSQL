### PremierLeagueSQL

<img src="/img/img.png" height="438" width="770" />

* Select 1
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
