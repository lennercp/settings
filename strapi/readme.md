## Criar um novo app

```bash
  npx create-strapi-app .
```

## Elephant SQL

**database name** é o **User & Default database**
**host** é o **tuffi.db.elephantsql.com**
**port** é 5432
**username** é o **User & Default database**
**password** é **password**
**enable SSL connection** y

#### Abrir a pasta e ir em config/database.ts e mudar no **pool** do postgres para ser de 0 a 5 connection
