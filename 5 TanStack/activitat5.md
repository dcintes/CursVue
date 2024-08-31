# Activitats 5

Implementar dos composables que faci ús de useQuery, el primer, per a obtenir un llistat i, el segon, un únic element d'una API i emmagatzemar aquestes dades a una store Pinia ORM
1. Composable: ha de retornar, com a mínim, el llistat o l'element (desde Pinia ORM) i isFetching
1. Pinia ORM: El model no fa falta tengui totes les propietats però si més de 6 i almenys una relació
1. API: Podeu fer la petició a https://api.spacexdata.com/v4/rockets i https://api.spacexdata.com/v4/rockets/<ID>. Heu de fer la petició amb axios directament (no usar imasaxios o utilitats pre-existents)
1. useQuery: Les peticions s'han de guardar en cache usant useQuery


El llistat s'ha de mostrar, amb un disseny simple, a una vista i al costat de cada element un enllaç a una altre vista que mostrarà un únic element per ID. Crear els components i rutes necessaris.
