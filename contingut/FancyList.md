Llista item-valors de forma horitzontal

![image](uploads/bb5c7112369e3b0ff931141d3f966cd3/image.png)


## Props

|Nom|Tipus|defecte|observacions|
|-|-|-|-|
|items|FancyItem[]||Llistat d'elements a pintar|
|bordered|boolean|false|Mostra un border envoltant els elements|
|separator|boolean|false|Mostra un separador entre cada element|

## FancyItem

|Nom|Tipus|defecte|observacions|
|-|-|-|-|
|label|string||Etiqueta que es mostrarà
|value|any||Valor a mostrar
|side|any||Valor de l'element side, si no s'estableix no es mostra|
|slot_key|string||Nom del slot si es vol definir el side amb un slot. S'ha de definir l'slot en qüestió|

## Slots

|Nom|observacions|
|-|-|
|actions|Permet afegir accions al final del llistat|
|side-<slot_key>|Sobreescriu el side d'un item en concret. S'ha de definir el slot_key|

```ts
 <FancyList
      class="col-3"
      :bordered="true"
      :items="[
        { label: 'asd', value: 'ssss', side: 'aaaaaa', slot_key: 'a' },
      ]"
    >
      <template v-slot:side-a="{ val }"> {{ val }} fff </template>
    </FancyList>
```