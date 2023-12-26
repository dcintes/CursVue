Per a configurar el ci per a automatitzar el pas a pre del frontend hem de crear i/o configurar els fitxers següents amb les dades que ens proporciona sistemes:

- ruta: normalment coincideix amb el codi d'aplicació
- serverpre: servidor de pre on es desplega l'aplicació
- serverpro: servidor de pro on es desplega l'aplicació
- clau: clau per poder accedir amb l'usuari al repositori git desde el servior.

1. Afegir la clau que ens proporciona a la configuració del projecte al git

![image](uploads/c4993feb83b351be2fdb711431c0a5c0/image.png)

2. Crear el fitxer `.gitlab-ci.yml` amb el següent contingut:

```yml
stages:
  - build

build_pre:
  stage: build
  script:
    - yarn install
    - cp .env.pre .env
    - yarn build
    - rsync -rav --delete dist/ www-data@<serverpre>.imasmallorca.net:/var/www/frontend/
  environment:
    name: pre
    url: https://proves.imasmallorca.net/<ruta>
  only:
    - pre
build_pro: # en el pas a pre es pot comentar aquest apartat
  stage: build
  script:
    - yarn install
    - cp .env.pro .env
    - yarn build
    - rsync -rav --delete dist/ www-data@<serverpro>.imasmallorca.net:/var/www/frontend/
  environment:
    name: pro
    url: https://intranet.imasmallorca.net/<ruta>
  only:
    - master
```

3. Crear i configurar els fitxers `.env.pre` i `.env.pro`, especialment el paràmetre `VITE_APP_API_URL` amb la ruta adequada i `VITE_APP_ENV` amb l'entorn corresponent

```js
VITE_APP_API_URL="https://proves.imasmallorca.net/<ruta>/api/"
VITE_APP_SECIM_URL="https://pre-imas.sedipualba.es/segex/tramite.aspx?idtramite="
VITE_APP_KEYCLOAK_URL="https://loginpre.imasmallorca.net/auth"
VITE_APP_KEYCLOAK_REALM="imaspre"
VITE_APP_KEYCLOAK_CLIENT="apps-imaspre"
VITE_APP_ENV="pre" // pre|pro
```

4. Podem pujar els canvis i realitzar un merge a la branca pre per a que s'executi

### Explicació script

```bash
# instala les llibreries
yarn install

# genera el fitxer .env amb el contingut de .env.pre o .env.pro
cp .env.pre .env

# compila el projecte per a producció (es genera dins la carpeta disst)
yarn build

# es connecta amb el servidor i copia el contingut compilat de dist a la carpeta de deploy del servidor
rsync -rav --delete dist/ www-data@<serverpre>.imasmallorca.net:/var/www/frontend/
```