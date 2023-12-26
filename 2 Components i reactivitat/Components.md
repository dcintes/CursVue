Els components son elements, normalment visuals, que ens permeten separar i reutilitzar part del codi. El framework Quasar ens aporta un conjunt de components que podem usar, apart de definir els propis de l'aplicació.

## Basics

- Els components s'han de definir dins una carpeta components dins cada mòdul. En cas de ser genèric dins la carpeta components del mòdul shared.
- Les rutes no han d'apuntar directament a un component.
- El nom del component ha de ser descriptiu i ha de contenir més d'una paraula. Ex: ExpedientFormDialog, ExpedientCard, ExpedientList...

## Interacció entre components

Els components habitualment es comuniquen entre ells per compartir dades, més enllà d'usar una store per certs recursos.

Disposam de tres formes principals: Props, Emits i Exposes

#### Props

Les propietats son valors de lectura que envia un component pare al fill.

Definició al component fill
```typescript
interface Props {
  expedient: Expedient
  actiu?: boolean //paràmetre opcional
}

const props = defineProps<Props>()
/*
// opcionalment podriem definir valors per defecte
const props = withDefaults(defineProps<Props>(), {
  actiu: true
})
*/
```
Al component pare fariem la cridada al component fill
```html
<ExpedientCard :expedient="expedient" :actiu="true" />
```

Les props no son reactius per defecte, per tant si canvien no s'actualitzen. Una solució podria ser usar toRef per convertir una propietat a ref
```typescript
const expedient = toref(props, 'expedient')
```

#### Emits

Si volem retornar valors al component pare hem d'usar els emits

Al component fill definim els emits. Aquest ha de tenir un nom i una o més variables
```typescript
interface Emits {
  (e: 'update', data: Expedient): void
}
const emit = defineEmits<Emits>()

// En el moment d'actualitzar les dades hem de cridar l'emit
emit('update', expedient.value)
```
Al component pare hem de definir un mètode que s'executarà quant el fill cridi l'emit
```html
<ExpedientCard @update="actualitzaExpedient"/>
```

#### Expose

Finalment tenim els expose que ens permet executar un mètode dins un component fill

Definim l'expose al component fill
```typescript
const open = (_expedient: Expedient) => {
  expedient.value = Expedient.from(_expedient)
  dialog.value = true
}

defineExpose({ open })
```

Al component pare hem de definir un ref i cridar el mètode
```html
<ExpedientFormDialog ref="expedientFormDialog" />
```
```typescript
// instanciam la variable, ha de tenir el mateix nom que el ref del component
const expedientFormDialog = ref()

// El moment de voler cridar el mètode
expedientFormDialog.open(expedient.value)

```
