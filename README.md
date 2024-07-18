# mongo

Pasos para la configuración de administradores en MongoDB:

Paso 1:
En la ruta `/etc/mongod.conf`, la seguridad viene desactivada por defecto. Localice el apartado de seguridad y reemplácelo por lo siguiente:

```yaml
security:
  authorization: enabled
```

Paso 2:
Reinicie el servicio de MongoDB:

```bash
sudo systemctl restart mongod
```

Paso 3:
Abra MongoDB Compass y realice la conexión normalmente. Si no se ha configurado previamente un usuario, use las credenciales por defecto proporcionadas durante la instalación inicial.

Paso 4:
En la consola de Compass, cree el usuario root:

```javascript
use admin

db.createUser({
   user: "root",
   pwd: passwordPrompt(),
   roles: [ { role: "root", db: "admin" } ]
})
```

Nota: Asegúrese de guardar las credenciales de acceso en un lugar seguro, ya que después de ejecutar este comando perderá acceso al cluster.

Paso 5:
Realice una nueva conexión con el siguiente string:

```
mongodb://adminUser:<password>@localhost:27017/
```

Paso 6:
En la consola de Compass, cree el rol y usuario administrador para una única base de datos:

```javascript
db.createRole(
  {
    role: "camper",
    privileges: [
      {
        resource: { db: "", collection: "" },
        actions: ["find", "insert", "update", "remove"]
      },
      {
        resource: { db: "", collection: "system.users" },
        actions: ["find", "insert", "update", "remove"]
      },
      {
        resource: { db: "", collection: "system.roles" },
        actions: ["find", "insert", "update", "remove"]
      },
      {
        resource: { db: "", collection: "" },
        actions: ["createRole", "dropRole", "grantRole", "revokeRole", "createUser", "dropUser", "createDatabase", "find", "createCollection", "dbStats", "collStats", "listCollections" ]
      }
    ],
    roles: []
  }
)

db.createUser(
  {
    user: "camper",
    pwd: "campus2023",
    roles: [
      { role: "camper", db: "admin" }
    ]
  }
)
```

Nota: Asegúrese de tener creada la base de datos a la cual va a asignar el usuario. Tenga en cuenta que las colecciones creadas bajo el usuario root serán accesibles únicamente por el usuario root.

```
db.grantRolesToUser("camper", ["camper"])

```

Paso 7:
Realice una nueva conexión con el siguiente string:

```
mongodb://<nombre_de_usuario>:<password>@localhost:<port>/nombre_base_de_datos
```

Nota: Este nuevo usuario tiene acceso total únicamente a la base de datos específica asignada.
