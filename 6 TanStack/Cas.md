Esquema del funcionament

![fluxeUseQuery.drawio](uploads/50959ab6d2cbc3fd0c53a37bad948020/fluxeUseQuery.drawio.png)

Una serie d'exemples explicats per a realitzar les peticions a l'API

## Element per ID
```typescript

const useExpedient = (idExpedient: number) => {

  // Calcul de l'element a mostrar
  const expedient = computed(() =>
    ExpedientRepo
      .with('familia')
      .find(idExpedient)
  )

  // Petició amb useQuery
  const { isFetching, isError } = useQuery<Expedient>(
    ['expedient', idExpedient],
    async () => {
      // Petició axios
      const data = await get<Expedient>(
        `/expedient/${idExpedient}/procediments`,
        'expedient'
      )
      // Guardam el resultat al piniaORM
      ExpedientRepo.save(data)
      return data
    }
  )

  // exposam l'entitat i el fetching
  return {
    expedient,

    isFetching,
    isError,
  }
}

export default useExpedient
```

El funcionament al instanciar el composable seria el següent:
1. S'executaria el computed de l'entitat i si ja tenim el valor a l'store el retornaria inmediatament
1. S'executaria el use query amb distintes possibilitats:
   * Tenim la cache de l'element activa -> No faria cap petició a l'api.
   * La cache ha caducat o es inexistent -> Realitzaria la petició a l'api
     1. Si el valor que ens retorna l'api ha canviat s'actualitzaria automàticament el computed de l'entitat.


## Llistat l'elements

El llistat d'expedients es basa en el mateix principi que l'anterior, però amb la dificultat del filtre

```typescript
// Definim un filtre
export interface ExpedientFiltre {
  idUsuari?: number
  idEstats?: number[]
}

const useExpedients = (_filtre: ExpedientFiltre = {}) => {
  // Inicialitzam el filtre
  const filtre = ref<ExpedientFiltre>(_filtre)

  // opcional, podem desactivar l'execució automàtica si el filtre es incomplet
  const enabled = computed(
    () => !!filtre.value.idEstats || !!filtre.value.idUsuari
  )

  // Computed amb el llistat d'expedients
  const expedients = computed(() => {
    const query = ExpedientRepo.query()

    // Aquí s'ha d'aplicar el filtre per a retornar els valos correctes
    if (filtre.value.idUsuari) {
      query.where('idUsuari', filtre.value.idUsuari)
    }

    return query.get()
  })

  // UseQuery amb la petició ajax
  const { data, isFetching, isError, refetch } = useQuery<Expedient[]>(
    ['expedients', filtre],
    async () => {
      const data = await get<Expedient[]>(
        `/expedients`,
        'expedients',
        filtre.value
      )
      ExpedientRepo.save(data)
      return data
    },
    {
      enabled: enabled, // opcional, si volem desactivar la petició en certes situacions
    }
  )

  return {
    expedients,
    filtre,

    isFetching,
    isError,

    refetch,
  }
}

export default useExpedients
```
El funcionament igual que en el cas anterior:
1. S'executaria el computed del llistat i retornarà els valos que compleixin el filtre. Poden ser tots o únicament els que tenim carregats
1. S'executaria el use query amb distintes possibilitats:
   * Tenim la cache la petició amb el filtre -> No faria cap petició a l'api.
   * La cache ha caducat o es inexistent -> Realitzaria la petició a l'api
     1. Si el valor que ens retorna l'api ha canviat o s'han afegit registres s'actualitzaria automàticament el computed del llistat.

En aquest cas hi ha la possibilitat de tenir una part dels elements en memoria local, per tant l'usuari veurà intantaniament uns elements, però al completar-se l'execució de l'api acabin d'apareixer els que falten.

En relació al filtre hi ha diverses aproximacions que son situacionals. En certs casos el back ja realitzarà un filtratge i tenim l'opció d'aprofitar aquest filtre amb la variable data o de replicar el mateix filtre al front. Per exemple, si tenim un filtre d'elemens actius o no actius segurament es més senzill replicar aquest where al front. Per contra, si es realitza un mega filtre al back podem aprofitar-ho de la següent manera:
```typescript
query.whereIn('id', data.map(d => d.id)
```
Amb el whereIn filtrarem tots els elements d'un llistat, en el cas de data son els que ens retorna l'api en cada consulta

Un altre punt important es el filtre. UseQuery llanzarà una nova petició cada vegada que el filtre canvia, per això es important que el filtres que s'usa al key de useQuery contengui únicament els elements que el back filtrarà. Si el nostre filtre conté més elements (per que realitzam un filtratge el front) podem fer el següent:
```typescript
const filtre = ref<ExpedientFiltre>(_filtre)
const filtreBack = computed(() => {
  idEstats: filtre.value.idEstat
})
```
El filtreBack seria el que usariem en el key de useQuery.

Una altre problemàtica que podem tenir amb el filtre, si l'usam directament a un formulari, és que cada vegada que l'usuari el modifica (escriu una lletra, selecciona un valor...) es llanci una petició a l'api. Per això hem de clonar el filtre i tornar i en pitjar el botó de cerca tornar a copiar-oh.
```typescript
const {filtre,....} = useExpedients()
const filtreForm = ref({...filtre.value})

const filtrar = () => {
  filtre.value = {...filtreForm.value}
}
```

## Create, update i delete element
```typescript
// Opcions per executar una acció desde forma al completar una actualització
interface Options {
  onSuccess?
  onError?
  onDelete?
}

const useExpedientUpdate = (options?: Options) => {
  const toast = useToast()
  const { t } = useI18n()

  // Creació i modificació
  const { mutate, isLoading, isSuccess, isError } = useMutation<
    Expedient,
    unknown,
    Expedient
  >(
    async (expedient) => {
      // Calculam url i metode en funció de si es creació o modificació (si te o no id)
      const url = expedient.id ? `/expedient/${expedient.id}` : '/expedient'
      const method = expedient.id ? Method.PUT : Method.POST
      const data = await save<Expedient>(
        url,
        'expedient',
        Expedient.from(expedient),
        method
      )
      // Si es ok guardam el valor a piniaORM
      ExpedientRepo.save(data)
      return data
    },
    {
      onSuccess: (data, variables) => {
        // Missatge de succes
        if (!variables.id) {
          toast.success(
            t('ui.msg.createSuccess', { el: data.numero_expedient })
          )
        } else {
          toast.success(
            t('ui.msg.updateSuccess', { el: data.numero_expedient })
          )
        }

        options?.onSuccess(data)
      },
    }
  )

  // Per a l'eliminació
  const { mutate: esborra, isLoading: isDeleting } = useMutation<
    void,
    undefined,
    number
  >(
    async (expedientId) => {
      // petició axios i si es ok borram l'element de piniaORM
      await del(`/expedient/${expedientId}`)
      ExpedientRepo.destroy(expedientId)
    },
    {
      // Missatge de borrat correcte
      onSuccess: (data, variables) => {
        toast.success(t('ui.msg.deleteSuccess', { el: t('exp.lexpedient') }))

        options?.onDelete(data)
      },
    }
  )

  // Exposam les variables següents
  return {
    update: mutate,
    isUpdating: isLoading,
    isUpdatingSuccess: isSuccess,
    isUpdatingError: isError,

    esborra,
    isDeleting,
  }
}

export default useExpedientUpdate
```

La forma d'usar això es molt senzilla:
```typescript
const {update, esborra} = useExpedientUpdate({
  onSuccess: () => { //coses },
  onDelete: () => { //coses }
})

const crear = (expedient: Expedient) {
  update(expedient)
}

const actualitzar = (expedient: Expedient) {
  update(expedient)
}

const eliminar = (expedient: Expedient) {
  esborra(expedient.id)
}
```