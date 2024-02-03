## Redux

El patró de disseny redux intenta solventar la problemàtica dels frameworks basats en components on s'ha de passar informació d'un component a un altre mitjançant propietats. Actualitzar aquesta informació a un component implica definir tota una lògica per a actualitzar la mateixa informació a la resta de components.

La nova aproximació es basa en la creació d'un o diversos store on guardar i centralitzar la informació i d'aquesta manera disposar únicament d'una copia de les dades i no una per component.

![redux-article-3-03.svg](uploads/514dbd831a99ab91c600b7479094ec44/redux-article-3-03.svg)

Dins vue disposam de la llibreria Pinia per a implementar aquesta aproximació.

## Pinia

https://pinia.vuejs.org/

Pinia es la llibreria recomanada per Vue per a la implementació de les stores en aquest framework. Un store defineix una lògica i exposa unes propietats i mètodes(actions) per a interactuar.

La forma de definir una store es senzilla. Com a convenció el nom de la store es "useNomStore"
```typescript
export const useAppStore = defineStore('app', () => {
  const currentUser = ref<Usuari>(Usuari.from(null))

  const setUser = (user) => {
    currentUser.value = user
  }

  return { 
    // Propietats
    currentUser

    // Metodes
    setUser
  }
})
```

La forma de cridar aquesta store es senzillament cridant l'export que hem definit. Hem de tenir clar que sempre que cridem a una store obtindrem la mateixa i actualitzar les dades a un els actualitzarà a la resta.
```typescript
const appStore = useAppStore()

// Per a accedir a una propietat del store
appStore.currentUser

// També es poden convertir a propietats reactives:
const { currentUser } = storeToRefs(appStore)
```

## Pinia ORM

Pinia ORM simula una "base de dades" usant les store com a base. El funcionament es molt semblant al de laravel amb la base de dades.

A la documentació oficial podem veure les opcions disponibles: https://pinia-orm.codedredd.de/