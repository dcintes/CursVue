Genera el llistat de d'elements per ser usats en un select juntament amb una funció per filtrar

## Options

|Nom|Tipus|defecte|observacions|
|-|-|-|-|
|repo|Repositori, ref o computed||Repo(s'agafen tots) o llistat d'elements|
|field|string o funció|'nom'|nom de la propietat o funció* per definir el label|
|id|string|'id'|nom de la propietat que serà el value
|searchField|string o funció|el valor definit a field|Nom de la propietat o funció* per definir el valor de cerca|

*La funció reb com a paràmetre d'entrada un element

## Exposa

|Nom|Tipus|observacions|
|-|-|-|
|data|array|llistat d'elements FilterModel filtrats|
|filter|funció|Funció per passar al q-select|

#### FilterModel
|Nom|Tipus|observacions|
|-|-|-|
|label|string|Valor calculat del label|
|search|string|String de cerca normalitzat|
|value|any|Valor definit per parametre id|
|object|any|Objecte de l'entitat|