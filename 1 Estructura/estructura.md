Aquest esquelet te predefinida una estructura de carpetes que s'ha de respectar per a mantenir un ordre. Partirem del contingut que es troba dins la carpeta `src` ja que tot el que hi ha fora es configuració base.

## Estructura de carpetes

* assets
  * img
  * scss
* config
* core
* locales
* models
* modules
  * shared
  * modul[1-n]
    * components
    * composables
    * routes
    * views
* store

#### Assets

Dins la carpeta assets podem pujar imatges del nostre projecte i afegir o completar els css si es necessari.

#### config

**index.ts**: conte la configuració base de l'aplicació, al iniciar una nova aplicació haurem d'ajustar aquesta configuració al nostre projecte.

**menu.ts**: Els elements d'aquest arxiu defineixen al menú autogenerat de l'aplicació.
```typescript
{
  title: tc('inici'), // títol del menu
  to: { name: 'Home' }, // View que obrirà
  icon: 'fas fa-home', // Icona
  children: [...] // En cas de tenir subnivells. Si s'emplena aquest no emplenar el to
  active: () => {...} // Si volem mostrar un element en una condició. Per exemple rols distints.
},
```

**routes.ts**: Aquí definirem les rutes que disposarà la nostra aplicació. Com veurem més endavant, als moduls es definiran les rutes propies que s'hauran d'importar aquí:

```typescript
{
  path: '',
  component: Home,
  name: 'Home',
},
...demoRoutes,
```

#### core

No s'hauria de tocar res d'aquesta carpeta, conte la configuració que arranca el projecte. En cas d'un error comentar-ho abans modificar res.

#### locales

Un fitxer json per a cada idioma disponible a l'aplicació. L'entrada errors tradueix automàticament els codis d'errors retornats del back, per exemple l'entrada "errors.imas_no_permisos" traduirà l'error imas_no_permisos retornat per el back. Intentar estructurar minimament les entrades, per exemple per entitats o mòduls

#### models

La carpeta models conte els models de l'aplicació en format Pinia ORM i un index.ts amb el llistat de repositoris

```typescript
// Exemple de model
export enum Especialitat {
  ...
}

export class Familia extends Model {
  static entity = 'Familia'

  @Attr(null) declare id: number
  @Str('') declare nom_familia: string
  @Num(null) declare idMunicipi: number

  @Attr(null) declare especialitat: Especialitat

  @Cast(() => DateCast)
  @Attr(null)
  declare created_at: Date

  @Cast(() => BooleanCast)
  @Bool(false)
  declare actiu: boolean

  @BelongsTo(() => Municipi, 'idMunicipi') declare municipi: Municipi

  @HasMany(() => Expedient, 'idFamilia') declare expedients: Expedient[]

  // Mètode per a crear una nova instancia detached
  static from(from: Familia | null): Familia {
    const to = new Familia()

    if (from) {
      Object.assign(to, {...from})
    }

    return to
  }

```

El index.ts simplement llistarà els repositoris disponibles per a no haver d'instanciar-ho cada vegada
```typescript
export const FamiliaRepo = useRepo(Familia)
....
```

#### modules

Carpeta més important del projecte on s'estructurarà el contingut de la nostra aplicació. Per a cada part diferenciada de la nostra aplicació es crearà un mòdul amb un nom identificatiu, per exemple l'aplicació d'adopcions es separa en els mòduls de "familia", "expedient" i "menor". Addicionalment disposarem d'un mòdul shared per a contingut genèric i comú a tota l'aplicació.

Cada mòdul s'estructurarà en varies carpetes:

**components**: Els components son fragments visuals reutilitzable.

**composables**: Fragments de lògica o codi no visual reutilitzable. (Es detallarà en un altre apartat)

**routes**: Rutes propies del mòdul que s'han d'importar (com a conjunt) dins les rutes de configuració.

**views**: Vistes o pàgines del mòdul. No son reutilizables i es corresponen 1 a 1 amb les rutes.

#### store

Com el seu nom indica, dins aquesta carpeta, es posaran les stores que siguin necessàries. (Es detallarà en un altre apartat)




