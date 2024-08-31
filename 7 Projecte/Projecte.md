# Projecte final

El projecte final consistirà en fer un crud per les entitats expedient i usuari.

Per a fer de servidor teniu una carpeta adjunta, al tema, `api-server` que desplega un servidor en local molt basic per a poder fer peticions. Dins la carpeta hi ha les instruccions per a inicialitzar el servidor.

El servidor permet models amb propietats dinàmiques, el mínim que haura de tenir cada model és:

Usuari
- nom: string
- username: string
- data_naixement: Date ISO

Expedient
- num: number
- data: Date ISO
- obert: boolean
- usuari_id: number

Es pot ampliar amb altres propietats que considereu. El format de data ISO es "2024-08-31T06:11:59.862Z", però no vos heu de preocupar, si la vostra propietat es un objecte tipus Date s'hauria de transformar automaticament en fer el POST o PUT.

S'han de crear dos mòduls, un per expedients i un altre per usuari. El mínim que han de tenir:

Usuari
- Vista llistat d'usuaris
  - Filtre per nom i username
- Vista un usuari
  - Expedients de l'usuari (hi ha d'haver un enllaç per anar a la vista de cada expedient)
- Formulari tipus dialog accessible des de el llistat per crear i de la vista d'usuari per modificar.

Expedient
- Vista llista expedients
  - Filtre per num expedient i obert o tancat (per defecte mostrar oberts)
- Vista un expedient
  - Boto "Veure usuari" per anar a la vista de l'usuari.
- Formulari tipus dialog accessible des de el llistat per crear i de la vista per modificar.

S'han de definir les rutes i menus necessaris. Es poder crear tots els components i composables que facin falta.

Requisits addicionals:
1. Les peticions s'han de fer usant les funcionalitats de TanStack
2. Les dades s'han de guardar amb Pinia ORM
3. Tots les vistes han de fer ús de components Quasar (Si dins una vista s'usa un component propi i aquest usa quasar també conta)

Es valorarà l'ús correcte de `components` i `composables`