Els composables son fragmets de lògica de codi reutilitzable que no tenen un component gràfic. Per exemple el podriem usar per fer les cridades a l'api.

#### Estructura bàsica

- Els composables han d'anar dins la carpeta composable del mòdul en qüestió i en el seu defecte dins shared
- El nom del composable ha de començar per use. Ex: useExpedient, useFitxer...
- El composable ha de tenir un return amb les propietats i mètodes que exposa

L'estructura bàsica es molt semblant a uns store de pinia
```typescript
const useExpedient = (parametres) => {
  // definició de variables i mètodes
  const expedient = ref<Expedient>()

  const setExpedient = (_expedient) => {
    expedient.value = _expedient
  }

  return {
    // parametres i mètodes
    expedient,
    setExpedient
  }
}
export default useExpedient
```

Al voler usar un composable simplement l'hem de cridar
```typescript
 const { expedient, setExpedient } = useExpedient()
```