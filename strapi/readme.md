[Elephantsql](#elephant-sql)
[Cloudinary](#cloudinary)

## Criar um novo app

```bash
  npx create-strapi-app .
```

## Elephant SQL

**database name** é o **User & Default database** <br>
**host** é o **tuffi.db.elephantsql.com**<br>
**port** é 5432<br>
**username** é o **User & Default database**<br>
**password** é **password**<br>
**enable SSL connection** y

#### Abrir a pasta e ir em config/database.ts e mudar no **pool** do postgres para ser de 0 a 5 connection

## Cloudinary

#### Install

```zsh
  npm install @strapi/provider-upload-cloudinary --save
```

#### Create config/plugins.ts

```ts
module.exports = ({ env }) => ({
  // ...
  upload: {
    config: {
      provider: "cloudinary",
      providerOptions: {
        cloud_name: env("CLOUDINARY_NAME"),
        api_key: env("CLOUDINARY_KEY"),
        api_secret: env("CLOUDINARY_SECRET"),
      },
      actionOptions: {
        upload: {},
        delete: {},
      },
    },
  },
  // ...
});
```

#### No .env

```env
CLOUDINARY_NAME = cloudinary-name
CLOUDINARY_KEY = cloudinary-key
CLOUDINARY_SECRET = cloudinary-secret
```

#### em config/middlewares.ts

```ts
{
  name: 'strapi::security',
  config: {
    contentSecurityPolicy: {
      useDefaults: true,
      directives: {
        'connect-src': ["'self'", 'https:'],
        'img-src': ["'self'", 'data:', 'blob:', 'res.cloudinary.com'],
        'media-src': ["'self'", 'data:', 'blob:', 'res.cloudinary.com'],
        upgradeInsecureRequests: null,
      },
    },
  },
},
```
