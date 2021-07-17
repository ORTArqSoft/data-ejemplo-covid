# data-ejemplo-covid
Ejemplo de un conjuntos de datos covid de personas y lugares visitados.

Comprobando los archivos
```
LOAD CSV FROM 'file:///lugares.csv' AS row
RETURN count(row) as cant_lugares
```
cant_lugares = 7

```
LOAD CSV FROM 'file:///personas.csv' AS row
RETURN count(row) as cant_personas
```
cant_personas = 22

```
LOAD CSV FROM 'file:///visita_lugares.csv' AS row
RETURN count(row) as cant_visita_lugares
```
cant_visita_lugares = 20

```
LOAD CSV FROM 'file:///visita_personas.csv' AS row
RETURN count(row) as cant_visita_personas
```
cant_visita_personas = 9

## subida de archivos en Neo4j
### personas
```
LOAD CSV WITH HEADERS FROM 'file:///personas.csv' as csv
FIELDTERMINATOR ';'
CREATE (p:Persona {
			id: csv.PersonaId, 
			nombre:csv.Nombre, 
			estadoDeSalud:csv.EstadoDeSalud, 
			diaHoraDetectado:datetime(csv.DiaHoraDetectado),
			direccion:point({x: toFloat(csv.DireccionLat), y: toFloat(csv.DireccionLong)})
		});
```

### lugares
```
LOAD CSV WITH HEADERS FROM 'file:///lugares.csv' AS csv
FIELDTERMINATOR ';'
CREATE (p:Lugar {
		id: csv.LugarId, 
		nombre:csv.Nombre, 
		direccion:point({x: toFloat(csv.DireccionLat), y: toFloat(csv.DireccionLong)})
	});
 ```
### index
```
CREATE INDEX ON :Lugar(id);
CREATE INDEX ON :Lugar(direccion);
CREATE INDEX ON :Lugar(nombre);
CREATE INDEX ON :Persona(id);
CREATE INDEX ON :Persona(nombre);
CREATE INDEX ON :Persona(estadoDeSalud);
CREATE INDEX ON :Persona(diaHoraDetectado); 
 ```
### relaciones visitas - lugares
```
LOAD CSV WITH HEADERS FROM 'file:///visita_lugares.csv' AS csv
FIELDTERMINATOR ';'
MATCH (p:Persona {id:csv.PersonaId}), (l:Lugar {id:csv.LugarId})
CREATE (p)-[rv:VISITA {id:csv.VisitaId, horaInicio:datetime(csv.HoraInicio), horaFin:datetime(csv.HoraFin)}]->(l)
SET rv.duracion=duration.inSeconds(rv.horaInicio, rv.horaFin);
```
### relaciones visitas - personas
```
LOAD CSV WITH HEADERS FROM 'file:///visita_personas.csv' AS csv
FIELDTERMINATOR ';'
MATCH (p1:Persona {id:csv.Persona1Id}), (p2:Persona {id:csv.Persona2Id})
CREATE (p1)-[rv:VISITA {id:csv.VisitaId, horaInicio:datetime(csv.HoraInicio), horaFin:datetime(csv.HoraFin)}]->(p2)
SET rv.duracion=duration.inSeconds(rv.horaInicio, rv.horaFin);
```

Estos datos fueron tomados y adaptados de Rik en la direccion: https://blog.bruggen.com/2020/04/covid-19-contact-tracing-blogpost-part.html

 
