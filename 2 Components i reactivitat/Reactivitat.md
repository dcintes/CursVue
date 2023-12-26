La reactivitat es basa en el patró de disseny d'observables. Tradicionalment, quant es volia saber si hi havia un canvi en un element, s'havia de consultar periodicament demanant per nous canvis.

Aquest padró defineix una forma de comunicació distinta, on un observavor es subscriu a un observable i aquest observable notifica a l'observador els canvis.

## Reactivitat a Vue

La reactivitat a veu es molt senzilla i transparent. Declarant un tipus de variables com a reactives automàticament activarà els canvis a la resta d'elements reactius com templates o computed

Hi ha bastantes formes d'usar la reactivitat, a continuació es detallen les més comuns.

#### ref

Forma més bàsica de reactivitat, basta declarar una variable com a ref

```typescript
//const nomVariable = ref<typo>(valorInicial)
const nom = ref<string>('')
```

L'accés a les variables reactives al template es realitza indicant el nom. En canvi dins el codi s'ha de cridar la variable `.value`

#### computed

Les "variables" computed executen una funció per a calcular el seu valor i reacciones als canvis de les variables reactives que s'hi defineixen.

```typescript
// forma basica: Calculam el nom complet a partir del nom i llinatges de la persona. Notar que persona és tipus ref
const nom_complet = computed(() => {
  return persona.value.nom + ' ' + persona.value.llinatges
})

// Avançada: Hi ha una forma d'usar les propietats computed definint un get i un set
const edat = computed({
  get() {...}
  set(newVal) {...}
}
```

#### watch

Vue disposa d'un altre mecanisme per executar un codi si una variable reactiva canvia i es el watch. Al watch s'ha de definir un o multiples desencadenants, la funció a executar quant el desencadenant canvia i uns opcions de configuració

```typescript
watch(desencadenant, funcio, opcions)

watch(
    persona, // al modificar la persona s'executa
    () => { // funció a executar
      ...
    },
    { // opcions (opcionals)
      immediate: true, // destacar immediate que executarà el codi al inici encara que no hagi canviat
    }
  )
```
Notar que el desencadenant es una propietat reactiva (pot ser ref, computed...). En cas de voler observar una propietat no reactiva (per exemple les props d'un component) l'hem de transformar abans a reactiva o usar una funció en el seu lloc `() => props.persona`

#### toRef

La forma de convertir una propietat no reactiva a reactiva es mitjançant toRef.
```typescript
const persona = toRef(() => props.persona)
```
