# Adv. fullstack dev. docs

In this repository, you can find all the files and documentation for this course. The file is a guide for reference in combination with the material from the lessons.

Please create a pull-request for corrections, remarks or enhancements.

# Node.js

Gebruik als je kan steeds **nvm**. Maak dan ook een `.nvmrc`-file aan in de root van je project zodat het command `nvm use` de node-versie kan vinden. Zowel de front- als de backend maken we in node.js, dus het is een goed idee om die versies te 'managen'.

# Frontend

## TLDR; Checklist bij een nieuw project

- [ ] Installeer een project: `npm init vite@latest app-name -- --template vue-ts`.
- [ ] `.nvmrc`-file is aangemaakt. (Handig truckje: je kan makkelijk 'iets' wegschrijven via `>>`, dus in dit geval bv.: `node -v >> .nvmrc`)
- [ ] `.prettierrc` of `.editorconfig` is aanwezig voor een consistente codebase voor verschillende developers. Kies zelf wat je het handigste vindt.
      Onder `code/.prettierrc` vind je een voorbeeld-file met een config die ik handig vind.
- [ ] Plugins nodig? Installeer ze dan (zie plugins).

## Create a vue-app

We gebruiken [vite](https://vitejs.dev) om het project te maken en om een dev-server op te zetten.

```bash
npm init vite@latest app-name -- --template vue-ts
```

Als het project aangemaakt is, installeren we de dependecies en runnen we de dev-server.

```bash
cd app-name   # Dus de naam van je project
npm i         # Of npm i
npm run dev   # Of andere commands in package.json
```

## Vue plugins

De bouwstenen van het vue-ecosystem zijn de plugins. Hier staan de meest voorname plugins opgelijst. Voor de meer specifieke settings verwijs ik naar de labo's, dit kan handig zijn voor als je een project aanmaakt.

- **Router**: Naar verschillende pagina's gaan in JS.

  1. Installeer de router.

  ```bash
  npm i vue-router@4
  ```

  2. Maak een file aan om alle routes en de setup bij te houden:

  ```typescript
  // router.ts
  import { createRouter, createWebHistory } from 'vue-router';

  import Home from '../components/Home.vue';
  // ...

  const routes = [
    { path: '/', component: Home },
    // ...
  ];

  const router = createRouter({
    history: createWebHistory(),
    routes,
  });

  export default router;
  ```

  3. 'Koppel' de router aan je vue-app.

  ```typescript
  // main.ts
  import { createApp } from 'vue';

  import App from './App.vue';
  import router from './bootstrap/router'; // Here, router is inside a folder bootstrap

  const app = createApp(App);

  app.use(router);

  app.mount('#app');
  ```

- **Vuex**: Lokaal zaken bijhouden en opvragen in JS.

  1. Installeer de package.

  ```
  npm install vuex@next --save
  ```

  2. Voeg ook deze plugin toe aan de `main.ts` file:

  ```typescript
  // main.ts
  import { createApp } from 'vue';

  import App from './App.vue';
  import Vuex from 'vuex';

  const app = createApp(App);

  app.use(Vuex);

  app.mount('#app');
  ```

- **[TailwindCSS](https://tailwindcss.com)**: "The best CSS-framework."

  1. Installeer de vereiste packages.

  ```bash
  npm i -D tailwindcss@latest postcss@latest autoprefixer@latest
  ```

  2. Genereer de nodige configuration files.

  ```bash
  npx tailwindcss init -p
  ```

  3. Zorg dat je enkel de classes die gerbuikt worden in je build CSS-bundle houdt:

  ```javascript
  // tailwind.config.js
  module.exports = {
    // mode: 'aot',
    purge: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
    darkMode: 'media', // or 'media' or 'class'
    theme: {
      extend: {},
    },
    variants: {
      extend: {},
    },
    plugins: [],
  };
  ```

  4. Maak een CSS-file waarin je de Tailwind directives oproept en die je import in je `main.ts`.

  ```css
  <!-- assets/screen.css -->
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  ```

  ```typescript
  // main.ts
  import { createApp } from 'vue';

  import App from './App.vue';
  import 'assets/screen.css'; // Import the css-file.

  const app = createApp(App);

  app.mount('#app');
  ```

# Backend

## TLDR; Checklist bij een nieuw project

Ja, hier moeten we meer zelf doen dan bij een frontend-project. We vertrekken met niets en gaan gaandeweg kleine onderdelen toevoegen om alles krachtiger te maken.
Je zou deze aanmaak kunnen automatiseren met een tool als [yeoman](https://yeoman.github.io/generator/).

- [ ] Maak een folder aan waar je het express project wil aanmaken. Werk met git.
- [ ] We maken een package-file aan:
      Doe het via `npm init`, je kan stap per stap de gegevens kiezen.
      Dit kan via `npm init -y`. Dit maakt een leeg `package.json`-file aan die je dan zelf kan aanvullen.
      Uiteindelijk krijgen we een file met een gelijkaardige structuur:
- [ ] Nu beginnen we aan het echte werk: de npm-packages toevoegen die onze app runnen en onze dev-setup vereenvoudigen:
  - [ ] We gebruiken **typescript** in ons project, inwstalleer `npm i -D typescript` & `npm i -D tslint`.
        De flag `-D` zet het onder `devDependencies` zodat het niet in de build meegenomen wordt.
  - [ ] We also init the typescript config: `npx tsc --init`. We change the config-file to use the dist folder as output: `"outDir": "dist"`.
  - [ ] We also install a dependency to have a smooth dev-server: `npm i -D ts-node`.
  - [ ] We are making this backend using the Express framework: `npm i -S express`.
  - [ ] We will also install the typescript interfaces for Express: `npm i -D @types/express`.
- [ ] Met bovenstaande packages kunnen we de scripts aanvullen in `package.json`:

      "start": "tsc && node dist/app.js",
      "dev": "npx nodemon --watch 'server/**/*' --exec 'ts-node' server/app.ts",

  Let op, bovenstaande scripts gaan er van uit dat in de backend de file `app.ts` in een map server staat (dit gaan we meestal doen).

- [ ] Maak een folder server aan met een `app.ts` file.
- [ ] `.nvmrc`-file is aangemaakt.
- [ ] `.prettierrc` of `.editorconfig` is aanwezig voor een consistente codebase voor verschillende developers. Kies zelf wat je het handigste vindt.
      Onder `code/.prettierrc` vind je een voorbeeld-file met een config die ik handig vind.
- [ ] Nu kan je beginnen met coden. Een basis express-app ziet er als volgt uit:

  ```typescript
  // app.ts
  import express, { Request, Response } from 'express';

  // APP SETUP
  const app = express(),
    port = process.env.PORT || 3000;

  // MIDDLEWARE
  app.use(express.json()); // for parsing application/json

  // ROUTES
  app.get('/', (request: Request, response: Response) => {
    response.send(`Welcome, just know: you matter!`);
  });

  // APP START
  app.listen(port);
  console.info(`\nServer 👾 \nListening on http://localhost:${port}/`);
  ```

## GraphQL setup

### Database

Met welke server ga je verbinden? Werk je op [Atlas van Mongo](https://www.mongodb.com/cloud/atlas) of lokaal in Docker?

### GraphQL

#### Installation & packages

To work with graphQL, we'll need a couple of packages:

##### Database connection packages

- [ ] `npm i typeorm --save` Install the TypeORM packages to enable Object-relation mapping in typescript. This will create a link from code to the database structure.
  - [ ] `npm i reflect-metadata --save` We also need to install reflect-metadata to enable the use of decorators in the code, something this package uses.
  - [ ] `import "reflect-metadata"` import the package in app.ts **before any GraphQL code**.
  - [ ] `npm i @types/node --save-dev` Typescript support for node can be useful.
- [ ] `npm i mongodb@^3.6.0 --save` Next, we install the driver for our MongoDB.
      _Remark: the official website states that this is 'experimental' (08 / '21)._
- [ ] Next we **enable** a couple of options in our Typescript config (`tsconfig.json`):

  ```json
  "emitDecoratorMetadata": true,
  "experimentalDecorators": true,
  ```

##### GraphQL packages

- [ ] `npm i graphql class-validator type-graphql` These are the basic packages for any project with GraphQL in our Node.js environment.
- [ ] We'll change up the `tsconfig.js` for the new packages:

  - [ ] Keep an eye on this, it can be useful to keep the target as high as possible.
    ```json
      "target": "es2018" // or newer if your node.js version supports this`
    ```
  - [ ] Due to using the graphql-subscription dependency that relies on an AsyncIterator, we may also have to provide the esnext.asynciterable to the lib option:
    ```json
    "lib": ["es2018", "esnext.asynciterable"]
    ```

- [ ] `npm i --save express-graphql` Finally, we will add a package to view the data in the browser via Express.

#### Enable GraphQL

- [ ] In code, we will need `/resolvers` & `/entities`.

  - [ ] Entities: The core of our data. The strength of our setup is the fact that we annotate database columns & GraphQL properties in this one entity:

    ```typescript
    import { ObjectType, Field, ID, Float, InputType } from 'type-graphql';

    import {
      Entity,
      BaseEntity,
      ObjectIdColumn,
      Column,
      CreateDateColumn,
      UpdateDateColumn,
      DeleteDateColumn,
    } from 'typeorm';

    import { ObjectId } from 'mongoose';

    // ObjectType decorator, to tell that our class represent a Graphql object type
    @ObjectType()
    // Makes it possible to use the entire entity as an input instead of using seperate arguments for each property.
    // Has to be explicitly named differently than the entity itself
    @InputType('TestEntityInput')
    // Entity decorator, to tell that our class represent an database entity
    @Entity('TestEntities')
    export class TestEntity extends BaseEntity {
      // extend the BaseEntity to use methods like find, findOne...
      @Field(() => ID, { nullable: true }) // Field decorator, represent a Graphql field of our graphql object type
      @ObjectIdColumn() // Special decorator, to tell that this column represent an unique generated ID (in mongo)
      id: ObjectId;
      @Field()
      @Column()
      title: string;
      @Field()
      @Column()
      message: string;
      @Field({ nullable: true })
      @Column({ nullable: true })
      someOtherEntityId?: string; // Optional
      @Field(() => Float, { nullable: true })
      @Column({ nullable: true })
      someRandomNumber?: number; // Optional
      @Field({ nullable: true })
      @CreateDateColumn({ type: 'timestamp', nullable: true })
      createdAt?: Date;
      @Field({ nullable: true })
      @UpdateDateColumn({ type: 'timestamp', nullable: true })
      updatedAt?: Date;
      @Field({ nullable: true })
      @DeleteDateColumn({ type: 'timestamp', nullable: true })
      deletedAt?: Date;
    }
    ```

  - [ ] Resolvers: The controller of the data. Here we write logic to get stuff, to delete it, etc.

    ```typescript
    import { Resolver, Query, Mutation, Arg, Authorized } from 'type-graphql';
    import { getMongoManager, MongoEntityManager } from 'typeorm';

    import { TestEntity } from '../entities/TestEntity';

    /**
     *
     * @description Resolver behaves like a controller in REST.
     * A resolver holds all the queries and mutations we want to perform on an entity.
     * Is a singleton instance
     */
    @Resolver()
    export class TestEntityResolver {
      manager: MongoEntityManager = getMongoManager('mongodb');

      @Authorized('SOME_ROLE') // Optional role checking -> needs some customisation further on ;-)
      @Query(() => [TestEntity], { nullable: true })
      async getTestEntities(): Promise<TestEntity[]> {
        // Because we create the manager inside the resolver, we must prefix it with "this." to use it.
        const testEntities = await this.manager
          .find<TestEntity>(TestEntity)
          .then((d) => d);

        return testEntities;
      }

      @Query(() => TestEntity, { nullable: true })
      async getTestEntityById(
        @Arg('id') id: string,
      ): Promise<TestEntity | undefined | null> {
        return await this.manager.findOne<TestEntity>(TestEntity, id);
      }

      @Mutation(() => TestEntity)
      createTestEntity(@Arg('data') newTestEntityData: TestEntity): TestEntity {
        const testEntity = this.manager.create(TestEntity, newTestEntityData);
        this.manager.save(testEntity);
        return testEntity;
      }
    }
    ```

  - [ ] Our app depends on the connection with a Mongo database. We will create an async closure to enable async/await syntax:
    ```typescript
    (async () => {
      // All our code
    })();
    ```
  - [ ] We start with connecting to our database, here is an example config:

    ```typescript
    const conn: MongoConnectionOptions = {
      name: 'mongodb',
      type: 'mongodb',
      url: TODO_1, // Url to the database eg. mongodb://myDBReader:D1fficultP%40ssw0rd@mongodb0.example.com:27017/?authSource=admin
      useNewUrlParser: true,
      synchronize: true,
      logging: true,
      useUnifiedTopology: true,
      entities: [
        TODO_2 // Check if we are using the production env. or the dev-server
          ? `${__dirname}/entities/**/*.js`
          : `${__dirname}/entities/**/*.ts`,
      ],
      ssl: true,
    };
    ```

  - [ ] Now we have to wait for the connection to resolve before we can go on:
    ```typescript
    await createConnection(conn);
    ```
  - [ ] Now, we can start the app with all the resolvers. Note that this is also an async action.

    ```typescript
    /**
     *
     * @description create the graphql schema with the imported resolvers
     */
    let schema: GraphQLSchema = {} as GraphQLSchema;
    const createSchema = async () => {
      await buildSchema({
        resolvers: [
          TestEntityResolver,
          // All the other resolvers
        ],
      }).then((_) => {
        schema = _;
      });

      // GraphQL init middleware
      app.use(
        '/v1/', // Url, do you want to keep track of a version?
        graphqlHTTP((request, response) => ({
          schema: schema,
          context: { request, response },
          graphiql: true,
        })),
      );

      // APP START -> also covered in basic Express part
      app.listen(port);
      console.info(
        `\nWelcome 👋\nGraphQL server @ http://localhost:${port}/v1\n`,
      );
    };

    createSchema();
    ```

### GraphiQL

## gRPC setup

Todo
