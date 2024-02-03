TanStack useQuery és una llibreria que gestiona una cache per facilitar i optimitzar les peticions a la api. Aquesta llibreria es basa en el principi dels composables.

De forma general evita que una petició es torni a realitzar mentre la cache no hagi caducat (5 min). Es a dir, encara que l'usuari entri a la mateixa pantalla (o una pàgina diferent on s'usi la mateixa petició) la petició a base de dades no es tornarà a realitzar si no ha caducat.

## useQuery

Composable per a realitzar peticions de consulta de dades. Manté una cache i evita duplicar peticions.

```typescript
const { parametres_exposats } = useQuery<Entitat_retorn>([key], funcio_axios, params?);
```

#### Exemple get un registre
```typescript
const { isFetching, isError } = useQuery<Expedient>(
  ['expedient', idExpedient],
  async () => {
    // petició axios
    const data = await get<Expedient>(
      `/expedient/${idExpedient}/procediments`,
      'expedient'
    )
    // guardam dades al piniaORM
    ExpedientRepo.save(data)
    return data
  }
)
```

#### Exemple llistat amb filtre
Notar que al key es passa un objecte filtre, això genera que si els valors del filtre canvien si que realitzarà la petició. S'ha d'anar alerta amb filtres de text ja que al escriure una lletra podria realitzar una petició nova
```typescript
const { isFetching, isError, refetch } = useQuery<Expedient[]>(
  ['expedients', filtre],
  async () => {
    // petició axios
    const data = await get<Expedient[]>(
      `/expedients`,
      'expedients',
      filtre.value
    )
    // guardam dades a piniaORM
    ExpedientRepo.save(data)
    return data
  }
)
```
El mètode exposat refetch ens permet forçar la realització d'una petició indistintament de l'estat de la cache.

## useMutation
El composable mutation ens facilita les peticions de creació, actualització i eliminació de registres. No manté cache i s'executen sempre.

```typescript
const { parametres_exposades} = useMutation<Entitat_retorn,unknown,Entitat_entrada>(funcio_axios, params?)
```

#### Exemple Creació i modificació
```typescript
const { mutate, isLoading} = useMutation<Expedient,unknown,Expedient>(
  async (expedient) => {
    
    const url = expedient.id ? `/expedient/${expedient.id}` : '/expedient'
    const method = expedient.id ? Method.PUT : Method.POST
    // petició axios
    const data = await save<Expedient>(
      url,
      'expedient',
      Expedient.from(expedient),
      method
    )
    // s'ha guardat bé i guardam dades a piniaORM
    ExpedientRepo.save(data)
    return data
  },
  {
    onSuccess: (data, variables) => {
      // Si es ok missatge success
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
```
Si axios dona un error, el missatge d'error es automàtic

#### Exemple Eliminació
Feim un canvi de nom a les variables exposades ja que ho solem definir al mateix composable que la modificació
```typescript
const { mutate: esborra, isLoading: isDeleting } = useMutation<void, undefined, number>(
  async (expedientId) => {
    await del(`/expedient/${expedientId}`)
    ExpedientRepo.destroy(expedientId)
  },
  {
    onSuccess: (data, variables) => {
      toast.success(t('ui.msg.deleteSuccess', { el: t('exp.lexpedient') }))
      options?.onDelete(data)
    },
  }
)
```