Script per general el model de Pinia ORM a partir de la bd.

#### Consideracions:

- El nom de les taules esta en snake_case. Ex: r_intervencio_tipus
- Les relacions hasMany es retornen en camelCase (igualment s'ha de revisar ja que es complicat fer el plural)
- Es pressuposa l'estructura de l'entorn docker

#### Dependencies:

S'han d'instal·lar les dependències de pyton següents (`sudo apt install python3-pip`)

- pip install mysql-connector-python
- pip install python-dotenv

#### Execució
Es pot llanzar per a tota la bd o de taula en taula

`python3 model.py`
o
`python3 model.py t_persona`


#### Script

S'ha de copiar l'script a la carpeta `src/models` amb nom `model.py`

```py
# Dependencies:
# sudo apt install python3-pip
# pip install mysql-connector-python
# pip install python-dotenv

# Execució
# // Colocar a la carpeta models
# python3 model.py [nom_taula]
# // Si no s'especifica la taula genera tota la bd
# // Si ja existeix el fitxer el sobreescriu

import mysql.connector
from mysql.connector import Error
from dotenv import load_dotenv
import os
import sys
import re

TAULA = None
if len(sys.argv) > 1:
  TAULA = sys.argv[1]

dotenv_path='../../../backend/.env'
load_dotenv(dotenv_path=dotenv_path)

DB_CONNECTION = os.getenv('DB_CONNECTION')
DB_HOST = os.getenv('DB_HOST')
DB_PORT = os.getenv('DB_PORT')
DB_DATABASE = os.getenv('DB_DATABASE')
DB_USERNAME = os.getenv('DB_USERNAME')
DB_PASSWORD = os.getenv('DB_PASSWORD')

class Basedades:
  connection = None

  def __init__(self, host_name, port, user_name, user_password, db_name):
    try:
        self.connection = mysql.connector.connect(
            host=host_name,
            port=port,
            user=user_name,
            passwd=user_password,
            database=db_name
        )
        print(f"MySQL {db_name} Database connection successful")
    except Error as err:
        print(f"Error: '{err}'")

  def read_query(self, query):
    cursor = self.connection.cursor()
    result = None
    try:
        cursor.execute(query)
        result = cursor.fetchall()
        return result
    except Error as err:
        print(f"Error: '{err}'")
        print(f"Query: '{query}'")

def snakeToPascalCase(str):
  return str.replace("_", " ").title().replace(" ", "")

def pascalToCamelCase(str):
  return str[0].lower() + str[1:]

def normalitzaNomTaula(taula):
  return re.sub(r'^[tra]_', '', taula)

# Mètode per generar el model
def generaModel(con, taula):
  nom = snakeToPascalCase(normalitzaNomTaula(taula))

  columns = con.read_query(f"SELECT c.COLUMN_NAME, c.DATA_TYPE, c.COLUMN_TYPE  from INFORMATION_SCHEMA.COLUMNS c WHERE c.TABLE_SCHEMA='{DB_DATABASE}' and c.TABLE_NAME='{taula}'")
  fks = con.read_query(f"SELECT k.TABLE_NAME, k.COLUMN_NAME, k.REFERENCED_TABLE_NAME ,k.REFERENCED_COLUMN_NAME  from information_schema.KEY_COLUMN_USAGE k where k.TABLE_SCHEMA  = '{DB_DATABASE}' and (k.TABLE_NAME='{taula}' or k.REFERENCED_TABLE_NAME='{taula}') and k.REFERENCED_TABLE_NAME  is not NULL")

  f = open(f"{nom}.ts", "w")
  # dependencies
  f.write("import { Model } from 'pinia-orm'\n")
  f.write("import { BooleanCast, DateCast } from 'pinia-orm/dist/casts'\n")
  f.write("import { Attr, BelongsTo, BelongsToMany, Bool, Cast, Num, Str, HasMany } from 'pinia-orm/dist/decorators'\n")
  # dependencies model
  for fk in fks:
    t = fk[2] if fk[0] == taula else fk[0]
    nt = snakeToPascalCase(normalitzaNomTaula(t))
    f.write(f"import {{ {nt} }} from './{nt}'\n")

  for column in columns:
    pattern = re.compile(r"'([A-Za-z0-9]+)'")
    if column[1] == 'enum':
      f.write(f"\n")
      f.write(f"export enum {snakeToPascalCase(column[0])} {{\n")
      for val in re.findall(pattern, column[2]):
        f.write(f"\t{val} = '{val}',\n")
      f.write("}\n")

  f.write(f"\n")
  #class
  f.write(f"export class {nom} extends Model {{\n")
  f.write(f"\tstatic entity = '{nom}'\n")
  f.write(f"\n")

  # camps
  for column in columns:
    if(column[1]=='bigint' or column[1] == 'int'):
      f.write(f"\t@Num(null) declare {column[0]}: number\n")

    elif(column[1]=='varchar' or column[1] == 'text'):
      f.write(f"\t@Str('') declare {column[0]}: string\n")

    elif(column[1]=='timestamp' or column[1] == 'date'):
      f.write("\t@Cast(() => DateCast)\n")
      f.write("\t@Attr(null)\n")
      f.write(f"\tdeclare {column[0]}: Date\n")

    elif(column[1]=='tinyint'):
      f.write("\t@Cast(() => BooleanCast)\n")
      f.write("\t@Bool(false)\n")
      f.write(f"\tdeclare {column[0]}: Date\n")

    elif(column[1]=='enum'):
      f.write(f"\t@Attr(null) declare {column[0]}: {snakeToPascalCase(column[0])}\n")

  # relacions
  for fk in fks:
    f.write(f"\n")
    t = fk[2] if fk[0] == taula else fk[0]
    nt = snakeToPascalCase(normalitzaNomTaula(t))
    if fk[2] == taula:
      #HasMany
      f.write(f"\t@HasMany(() => {nt}, '{fk[1]}')\n")
      f.write(f"\tdeclare {pascalToCamelCase(nt)}s: {nt}[]\n")
    else:
      #BelongsTo
      f.write(f"\t@BelongsTo(() => {nt}, '{fk[1]}')\n")
      f.write(f"\tdeclare {pascalToCamelCase(nt)}: {nt}\n")

  #from
  f.write("\n")
  f.write(f"\tstatic from(from?: {nom}): {nom} {{\n")
  f.write(f"\t\tconst to = new {nom}()\n")
  f.write("\n")
  f.write("\t\tif (from) {\n")
  f.write("\t\t\tObject.assign(to, {...from})\n")
  f.write("\t\t} \n")
  f.write("\n")
  f.write("\t\treturn to\n")
  f.write("\t}\n")

  f.write("}\n")

connection = Basedades(DB_HOST, DB_PORT, DB_USERNAME, DB_PASSWORD, DB_DATABASE)

# Generam totes les taules
if TAULA is None:
  taules = connection.read_query(f"SELECT t.TABLE_NAME from information_schema.TABLES t WHERE t.TABLE_SCHEMA='{DB_DATABASE}'")
  for taula in taules:
    generaModel(connection, taula[0])
else:
  generaModel(connection, TAULA)
```