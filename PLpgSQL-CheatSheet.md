# PL/pgSQL

## STORED PROCEDURE

```sql
create or replace function square(integer) returns integer as $$
declare
  x integer := $1;
begin
  raise notice 'Meine erste Funktion!';
  x := x * x;
  raise notice 'Ergebnis: %', x;
  return x;
end;
$$ language plpgsql;
```
Aufruf:

```
stsaboja=> select square(5);
**NOTICE**: Meine erste Funktion!
**NOTICE**: Ergebnis: 25
```

## RAISE NOTICE

```sql
declare
  a integer := 4;
  b integer := 17;
begin
  raise info 'Ein Infotext';
  raise notice 'a: % b: %', a, b;
end;
```
