Component per generar la botonera d'un formulari. Si es troba dins un formulari realitza un submit al pitjar crear/guardar automaticament.

El botó d'eliminar demana confirmació en un popup.

Es poden ampliar els botons mitjançant un slot

## Props

|Nom|Tipus|defecte|observacions|
|-|-|-|-|
|nomEntitat|string|"registre"|Nom de l'entitat per mostrar als missatges
|showDelete|boolean|false|Mostra el boto d'eliminar
|isUpdate|boolean|true|Determina si es modificació, en cas contrari es considera creació
|isUpdating|boolean|false|Desactiva el boto de crear/actualitzar
|isDeleting|boolean|false|Desactiva el boto de eliminar

## Emits

|Nom|Propietats|observacions|
|-|-|-|
|update||Al clicar actualitzar, si es troba dins un form ja llança el submit|
|delete||Al confirmar l'eliminació|

## Slots

|Nom|observacions|
|-|-|
|moreButtons|Per afegir més botons|