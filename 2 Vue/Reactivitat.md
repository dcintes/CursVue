# Reactivitat

La reactivitat és una de les peces clau en aquest nou model de programació dinàmic. Entendre a la perfecció el seu funcionament és clau per a poder desenvolupar amb eficiència i poder comprendre els errors d'asyncronia que ens podem trobar.

## Observer pattern

La reactivitat es basa en el patró de disseny d'observables. Tradicionalment, quant es volia saber si hi havia un canvi en un element, s'havia de consultar periòdicament demanant per nous canvis.

Aquest patró defineix una forma de comunicació distinta, on un observavor es subscriu a un subjecte observable i aquest subjecte notifica a l'observador els canvis.

[Més informació](https://en.wikipedia.org/wiki/Observer_pattern)

## Reactivitat a Vue

La reactivitat a Vue és molt senzilla i transparent. Declarant un tipus de variables com a reactives automàticament activarà els canvis a la resta d'elements reactius com templates o computed.

Hem de tenir en compte que, per defecte, les variables normals, props... no són reactives, per tant, canvis en aquestes no es veuran reflectides de forma automàtica.

Hi ha bastantes formes d'usar la reactivitat, a continuació es detallen les més comuns.

### ref

Forma més bàsica de reactivitat, basta declarar una variable com a ref

```typescript
// const nomVariable = ref<typo>(valorInicial)
const nom = ref<string>('')
```

L'accés a les variables reactives dins el template es realitza indicant el nom directament. En canvi dins el codi s'ha de cridar la propietat `.value` de la variable.

```html
<!-- Dins el template-->
{{ nom }}
```

```typescript
// Al codi typescript
nom.value
```

### computed

Les "variables" computed executen una funció per a calcular el seu valor i reaccionen als canvis de les variables reactives que s'hi defineixen.

És a dir, es subscriuen als canvis de les variables reactives i executen la funció cada vegada que hi ha un canvi per a calcular el valor. El valor resultant també es reactiu.

```typescript
// Calculam el nom complet a partir del nom i llinatges de la persona. Notar que persona és tipus ref
const nomComplet = computed(() => {
  return persona.value.nom + ' ' + persona.value.llinatges
})
```

Per defecte, les variables reactives no ens permeten canviar el valor, són readonly. Existeix una forma avançada de poder fer-ho que consisteix en definir un objecte amb les propietats get i set.
```typescript
const variableNom = computed({
  get() {...}
  set(newVal) {...}
}
```

> **Cas pràctic**
> 
> El cas més útil per a usar aquesta forma avançada del computed és en combinació de les props, emits i la directiva v-model.

```typescript
const value = computed({
  get () {
    return props.modelValue
  },
  set (newValue) {
    emit('update:modelValue', newValue)
  }
})
```

> En aquest cas, la variable `value` agafa el valor de la propietat `modelValue` i cada vegada que es realitza un canvi a la variable value, s'actualitza de forma automàtica mitjançant l'emit. Això ens sinconitza la propietat del component pare i del fill de forma automàtica.

### watch

Vue disposa d'un altre mecanisme per executar un codi si una variable reactiva canvia i és el watch. Al watch s'ha de definir un o múltiples desencadenants, la funció a executar quant el desencadenant canvia i unes opcions de configuració

```typescript
watch(desencadenant, funcio, opcions)

watch(
    persona, // al modificar la persona s'executa
    () => { // funció a executar
      ...
    },
    { // opcions (opcionals)
      immediate: true, // destacar immediate que executarà el codi a l'inici encara que no hagi canviat
    }
  )
```
Notar que el desencadenant és una propietat reactiva (pot ser ref, computed...). En cas de voler observar una propietat no reactiva (per exemple, les props d'un component) l'hem de transformar abans a reactiva o usar una funció en el seu lloc `() => props.persona`

### toRef

La forma de convertir una propietat no reactiva a reactiva és mitjançant toRef. Aquesta es pot definir de múltiples formes:
```typescript
// Amb una funció
const persona = toRef(() => props.persona)
// Amb paràmetres
const persona = toRef(props,'persona')
```

### toRefs

Semblant a toRef, però convertint totes les propietats d'un objecte a reactives.

```typescript
const { persona, estat } = toRefs(props)
```
