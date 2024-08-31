# Activitats 4

Per a realitzar aquestes activitats haureu de definir un mòdul amb dues vistes, Home i Dades, i les rutes necessaries. S'ha d'afegir un enllaç al home que dugui directament a Dades. Es poden afegir components o composables si es necessari.

1. Definiu una store amb pinia per emmagatzemar les dades de l'usuari connectat (username, nom, correu, preferències...) i empleneu manualment les dades al carregar el home. Les dades s'han de visualitzar a les dues vistes i han de ser reactives als canvis. S'ha d'habitar alguna opció per modificar algunes dades.
1. Definiu un Model Pinia ORM (tematica lliure que tengui almenys un string, un boolean i un numeric), empleneu amb dades (més d'una) des de la vista Home i recuperau i mostrau aquestes a la vista Dades. Crear una acció per modificar alguna dada, per exemple incrementar el numèric o canviar el boolean d'estat. Les dades mostrades han de ser reactives i s'ha d'actualitzar el store.

Retornar un comprimit amb la carpeta del modul, el store pinia i el model Pinia ORM
