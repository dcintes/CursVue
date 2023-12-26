Component per generar una card per destacar dades

![image](uploads/44383d1b6547ac7fe4679843129cacad/image.png)

## Props

|Nom|Tipus|defecte|observacions|
|-|-|-|-|
|icon|string||Nom de l'entitat per mostrar als missatges
|icon_bg_color|string||Color de fons de l'icona
|icon_text_color|string|auto|color de l'icona
|content_bg_color|string|<parent>|color de fons del contingut
|content_text_color|string|auto|color de text del contingut
|title|string|false|titol del contingut
|value|string|false|valor del contingut
|value_class|string|text-h5|Classe per al valor

## Slots

|Nom|observacions|
|-|-|
|content|Sobreescriu el contingut per l'slot, els camps title i value no tenen efecte|
|actions|Afegeix una darrera columna per accions|

```vue
<template v-slot:actions>
  <q-btn icon="fas fa-eye"/>
</template>
```