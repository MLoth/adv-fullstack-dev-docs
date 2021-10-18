# Adv. fullstack dev. docs

In this repository, you can find all the files and documentation for this course. The file is a guide for reference in combination with the material from the lessons.

Please create a pull-request for corrections, remarks or enhancements.

# Node.js

Always use **nvm** when you can. Make sure to include a `.nvmrc`-file in tha case. Put it in the root of your project so that the `nvm use` command can find the right Node-version.
Both the frontend and backend will be written with NodeJS, so the best practice is to 'manage' their Node version.

# Frontend

## TLDR; Checklist for a new project

- [ ] Create a new project `npm init vite@latest app-name -- --template vue-ts`.
- [ ] Add a `.nvmrc`-file containing the Node version. Trick: `node -v >> .nvmrc` will writ the Node version to that file. It works cross-platform!
- [ ] `.prettierrc` or `.editorconfig` is available. This makes sure we have a consistent codebase for different developers. Choose something that you prefer yourself. You can find an example of a file in `code/.prettierrc` for something your teachers will use.

- [ ] Need more plugins? Just install them!

## Create a vue-app

We use [vite](https://vitejs.dev) to create the project and setup a development environment.

```bash
npm init vite@latest app-name -- --template vue-ts
```

As soon as the project is made we install the dependencies and run the development server.

```bash
cd app-name   # The name of your project
npm i         # Or npm install
npm run dev   # Or any other command inside package.json
```

## Vue plugins

The Vue-ecosystem consist mostly out of plugins. We have written the most important ones in here. For more specific settings, we refer to the lab assignments. But these general settings can be a great kickstarter for any new project.

- **Router**: Go to different pages inside JS

  1. Install the router.

  ```bash
  npm i vue-router@4
  ```

  2. Create a file to setup the router and keep track of different routes

  ```typescript
  // router.ts
  import { createRouter, createWebHistory } from 'vue-router'

  const routes: RouteRecordRaw[] = [
    { path: '/', component: () => import('../components/Home.vue')) },
    // ...
  ]

  const router: Router = createRouter({
    history: createWebHistory(),
    routes,
  })

  // Route guard
  router.beforeEach(
    (
      route: RouteLocationNormalized,
      from: RouteLocationNormalized,
      next: NavigationGuardNext,
    ) => {
      // Check a property that you added to the route meta
      if (route.meta.SOMETHING) {
        // If user is allowed
        if (userIsAllowed) {
          next()
        // Else redirect to error or path, etc.
        } else {
          router.push(from.path)
        }
      }
    },
  )

  export default router
  ```

  3. 'Link' the router to your Vue app.

  ```typescript
  // main.ts
  import { createApp, App as AppInterface } from 'vue'

  import App from './App.vue'
  import router from './bootstrap/router' // Here, router is inside a folder bootstrap

  const app: AppInterface = createApp(App)

  app.use(router)

  app.mount('#app')
  ```

- **Vuex**: Keep track of data locally and refer to it in JS

  1. Install the package.

  ```
  npm install vuex@next --save
  ```

  2. Link the plugin-file inside your `main.ts` file:

  ```typescript
  // main.ts
  import { createApp, App as AppInterface } from 'vue'

  import App from './App.vue'
  import store from './store/' // Use of index.ts file in dir.

  const app: AppInterface = createApp(App)

  app.use(store)

  app.mount('#app')
  ```

  3. Create the store (index.ts), here with one module (moduleA):

  ```typescript
  import { createStore } from 'vuex'

  import moduleA from './modules/moduleA'

  const store = createStore({
    modules: {
      moduleA,
    },
  })

  export default store
  ```

  4. The real work, a Vuex module:

  ```typescript
  import { Commit } from 'vuex'
  import Entity from '../../models/Entity'

  type State = {
    someStateToKeep: { [Key in string]: Entity }
    someMoreStateToKeep: any[]
  }

  export enum MutationTypes {
    UPDATE_ENTITY = 'updateEntity',
    SET_API_DATA = 'setApiData',
  }

  export enum GetterTypes {
    GET_SOME_ENTITIES = 'getSomeEntities',
  }

  export enum ActionTypes {
    VERY_SLOW_ASYNC_CALL = 'verySlowAsyncCall',
  }

  const state: State = {
    someStateToKeep: {},
    someMoreStateToKeep: [],
  }

  export default {
    state: state,

    getters: {
      // Check if name exists in the state.
      [GetterTypes.GET_SOME_ENTITIES]: (state: State) => (entityName: string) =>
        state.someStateToKeep[entityName] ? true : false,
    },

    mutations: {
      [MutationTypes.UPDATE_ENTITY](state: State, e: Entity) {
        const position: number = state.someMoreStateToKeep.findIndex(
          (value: Entity) => value.id === e.id,
        )

        if (position >= 0) {
          state.someMoreStateToKeep[position] = e
        }
      },
    },

    actions: {
      async [ActionTypes.VERY_SLOW_ASYNC_CALL]({ commit }: { commit: Commit }) {
        // Can take some time
        const data = await fetch('https://data.org/slow-api').then((r) =>
          r.json(),
        )

        // This can be done instant, the data is here.
        commit(MutationTypes.SET_API_DATA, data)
      },
    },
  }
  ```

- **[TailwindCSS](https://tailwindcss.com)**: "The best CSS-framework."

  1. Install the necessary packages and dependencies for TailwindCSS.

  ```bash
  npm i -D tailwindcss@latest postcss@latest autoprefixer@latest
  ```

  2. Generate the necessary configuration files.

  ```bash
  npx tailwindcss init -p
  ```

  3. Make sure to only use the Classes that you really use in your built CSS-bundle.

  ```javascript
  // tailwind.config.js
  module.exports = {
    // mode: 'jit',

    purge: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],

    darkMode: 'media', // or 'class'

    theme: {
      extend: {},
    },

    variants: {
      extend: {},
    },

    plugins: [],
  }
  ```

  4. Create CSS-file where you call the Tailwind directives and import them into `main.ts`.

  ```css
  <!-- assets/screen.css - (PostCSS) -->
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  ```

  ```typescript
  // main.ts
  import { createApp, App as AppInterface } from 'vue'

  import App from './App.vue'
  import './assets/screen.css' // Import the css-file.

  const app: AppInterface = createApp(App)

  app.mount('#app')
  ```

- **PWA** support

  1. Install the packages for the pwa plugin vite and for the workbox plugin.

  ```bash
  npm i vite-plugin-pwa workbox-precaching -D
  ```

  2. Update the Vite-plugin file `vite.config.ts`:

  ```typescript
  import { defineConfig } from 'vite'
  import vue from '@vitejs/plugin-vue'
  import { VitePWA } from 'vite-plugin-pwa'

  export default defineConfig({
    plugins: [vue(), VitePWA()],
  })
  ```

  A complete config would look something like:

  ```typescript
  import { defineConfig } from 'vite'
  import vue from '@vitejs/plugin-vue'
  import { VitePWA } from 'vite-plugin-pwa'

  export default defineConfig({
    plugins: [
      vue(),
      VitePWA({
        mode: 'development',
        base: '/',
        srcDir: 'src',
        filename: 'sw.ts',
        includeAssets: ['/favicon.png'],
        strategies: 'injectManifest',
        manifest: {
          name: 'Test Project',
          short_name: 'Test',
          theme_color: '#ffffff',
          start_url: '/',
          display: 'standalone',
          background_color: '#ffffff',
          icons: [
            {
              src: 'icon-192.png',
              sizes: '192x192',
              type: 'image/png',
            },
            {
              src: '/icon-512.png',
              sizes: '512x512',
              type: 'image/png',
            },
            {
              src: 'icon-512.png',
              sizes: '512x512',
              type: 'image/png',
              purpose: 'any maskable',
            },
          ],
        },
      }),
    ],
  })
  ```

  3. Some typescript errors might appear, we can fix these by allowing workers:

  ```json
  {
    "compilerOptions": {
      "target": "esnext",
      "module": "esnext",
      "moduleResolution": "node",
      "strict": true,
      "jsx": "preserve",
      "sourceMap": true,
      "resolveJsonModule": true,
      "esModuleInterop": true,
      "baseUrl": ".",
      "paths": {
        "/@/*": ["src/*"]
      },
      "lib": ["ESNext", "DOM", "WebWorker"]
    },
    "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
    "exclude": [
      "dist",
      "node_modules",
      "test",
      "test.ts",
      "**/*.spec.ts",
      "**/*.worker.ts"
    ]
  }
  ```

  See also https://rubenr.dev/en/pwa-vite/ for service-worker implementation.

# Backend

## TLDR; Checklist bij een nieuw project

I'm sorry to tell you, but we'll have to do a little bit more custom setup for a backend project, than for the Vue frontend project.

We start from scratch building up the project with smaller parts, to make everything into a powerfull app. You could automate the setup process with a tool like [yeoman](https://yeoman.github.io/generator/).

- [ ] Create a folder where you want to setup the `Express` project. Use Git to version control it.
- [ ] Create a `package.json` file with `npm init`. Fill in the necessary details. You could also skip the Wizard (`npm init -y`), and create the file yourself.

Now we can start by adding some NPM-packages to our project. These will run our app and make sure we can develop in a smooth way.

- [ ] As we use **typescript** in our project, we have to install it with `npm i -D typescript`. Also install `npm i -D eslint`.  
       Note the `-D` flag. This will make sure the package is listed inside the `devDependencies` section of our `package.json` file. Any package in there will not be used inside the build process. We only need it during development.  
       If you used to work with `tslint`, you should change to `eslint` as the former is now deprecated and no longer supported.
- [ ] Install the Typescript config: `npx tsc --init`. We change the `"outDir": "dist"` to make sure we are using the `dist` directory as the output.
- [ ] To ensure a smooth development server, intall `npm i -D ts-node`.
- [ ] The backend uses Express: `npm i -S express`.
- [ ] For Typescript, we will install custom Express interfaces: `npm i -D @types/express`.
- [ ] Now that we have everything installed, we can configure our `package.json` with some scripts:

  ```json
  "start": "npx tsc && node dist/app.js",
  "dev": "npx nodemon --watch 'server/**/*' --exec 'ts-node' server/app.ts",
  ```

  Note that these scripts require the backend file `app.ts` resides in the `server` directory. (This is a best-practice).

- [ ] Create a folder `server` with the `app.ts` file.
- [ ] On some machines, it might occur that the previous config doesn't work smoothly. Try to change it to `"dev": "npx nodemon --watch server/**/* --exec ts-node server/app.ts",`. Now try and edit the `app.ts` file, and see if it automatically reloads your app without crashing.
- [ ] If you couldn't get it working, try and add a `nodemon.json` file containing this content.
  ```json
  {
    "watch": ["server"],
    "ext": "ts,json",
    "exec": "ts-node ./server/app.ts"
  }
  ```
- [ ] Now, do make sure you update the line in `package.json` to: `"dev": "nodemon",`
- [ ] Add a `.nvmrc`-file containing the Node version. Trick: `node -v >> .nvmrc` will writ the Node version to that file. It works cross-platform!
- [ ] `.prettierrc` or `.editorconfig` is available. This makes sure we have a consistent codebase for different developers. Choose something that you prefer yourself. You can find an example of a file in `code/.prettierrc` for something your teachers will use.
- [ ] You can also opt to use [es-lint](https://eslint.org/). This can work pretty good in combination with prettier and really enforce a way of writing code:

  - [ ] `npm install -D eslint eslint-plugin-vue` installs es-lint.
  - [ ] `npm install -D eslint-config-prettier` avoids a clash of titans with the prettier config.
  - [ ] Create a config file `.eslintrc.js`:

  ```javascript
  module.exports = {
    env: {
      node: true,
    },
    extends: [
      'eslint:recommended',
      'plugin:vue/vue3-recommended',
      '**prettier**',
    ],
    rules: {
      // override/add rules settings here, such as:
      // 'vue/no-unused-vars': 'error'
    },
  }
  ```

  - [ ] Add the linting scripts to `package.json`:

  ```
  "scripts":{
    //...
    "lint": "eslint --ext .js,.vue --ignore-path .gitignore --fix src",
    "format": "prettier .  --write"
  }
  ```

- [ ] Now start coding. A basic Express NodeJS app looks like this:

  ```typescript
  // app.ts
  import express, { Request, Response } from 'express'

  // APP SETUP
  const app = express(),
    port = process.env.PORT || 3000

  // MIDDLEWARE
  app.use(express.json()) // for parsing application/json

  // ROUTES
  app.get('/', (request: Request, response: Response) => {
    response.send(`Welcome, just know: you matter!`)
  })

  // APP START
  app.listen(port, () => {
    console.info(`\nServer 👾 \nListening on http://localhost:${port}/`)
  })
  ```

## GraphQL setup

### Database

What server are you going to connect with? Is it [Atlas by Mongo](https://www.mongodb.com/cloud/atlas), or perhaps a local DB in Docker? Or outside of your network?

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
    import { ObjectType, Field, ID, Float, InputType } from 'type-graphql'

    import {
      Entity,
      BaseEntity,
      ObjectIdColumn,
      Column,
      CreateDateColumn,
      UpdateDateColumn,
      DeleteDateColumn,
    } from 'typeorm'

    import { ObjectId } from 'mongoose'

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
      id: ObjectId
      @Field()
      @Column()
      title: string
      @Field()
      @Column()
      message: string
      @Field({ nullable: true })
      @Column({ nullable: true })
      someOtherEntityId?: string // Optional
      @Field(() => Float, { nullable: true })
      @Column({ nullable: true })
      someRandomNumber?: number // Optional
      @Field({ nullable: true })
      @CreateDateColumn({ type: 'timestamp', nullable: true })
      createdAt?: Date
      @Field({ nullable: true })
      @UpdateDateColumn({ type: 'timestamp', nullable: true })
      updatedAt?: Date
      @Field({ nullable: true })
      @DeleteDateColumn({ type: 'timestamp', nullable: true })
      deletedAt?: Date
    }
    ```

  - [ ] Resolvers: The controller of the data. Here we write logic to get stuff, to delete it, etc.

    ```typescript
    import { Resolver, Query, Mutation, Arg, Authorized } from 'type-graphql'
    import { getMongoManager, MongoEntityManager } from 'typeorm'

    import { TestEntity } from '../entities/TestEntity'

    /**
     *
     * @description Resolver behaves like a controller in REST.
     * A resolver holds all the queries and mutations we want to perform on an entity.
     * Is a singleton instance
     */
    @Resolver()
    export class TestEntityResolver {
      manager: MongoEntityManager = getMongoManager('mongodb')

      @Authorized('SOME_ROLE') // Optional role checking -> needs some customisation further on ;-)
      @Query(() => [TestEntity], { nullable: true })
      async getTestEntities(): Promise<TestEntity[]> {
        // Because we create the manager inside the resolver, we must prefix it with "this." to use it.
        const testEntities = await this.manager
          .find<TestEntity>(TestEntity)
          .then((d) => d)

        return testEntities
      }

      @Query(() => TestEntity, { nullable: true })
      async getTestEntityById(
        @Arg('id') id: string,
      ): Promise<TestEntity | undefined | null> {
        return await this.manager.findOne<TestEntity>(TestEntity, id)
      }

      @Mutation(() => TestEntity)
      createTestEntity(@Arg('data') newTestEntityData: TestEntity): TestEntity {
        const testEntity = this.manager.create(TestEntity, newTestEntityData)
        this.manager.save(testEntity)
        return testEntity
      }
    }
    ```

  - [ ] Our app depends on the connection with a Mongo database. We will create an async closure to enable async/await syntax:
    ```typescript
    ;(async () => {
      // All our code
    })()
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
    }
    ```

  - [ ] Now we have to wait for the connection to resolve before we can go on:
    ```typescript
    await createConnection(conn)
    ```
  - [ ] Now, we can start the app with all the resolvers. Note that this is also an async action.

    ```typescript
    /**
     *
     * @description create the graphql schema with the imported resolvers
     */
    let schema: GraphQLSchema = {} as GraphQLSchema
    const createSchema = async () => {
      await buildSchema({
        resolvers: [
          TestEntityResolver,
          // All the other resolvers
        ],
      }).then((_) => {
        schema = _
      })

      // GraphQL init middleware
      app.use(
        '/v1/', // Url, do you want to keep track of a version?
        graphqlHTTP((request, response) => ({
          schema: schema,
          context: { request, response },
          graphiql: true,
        })),
      )

      // APP START -> also covered in basic Express part
      app.listen(port, () => {
        console.info(
          `\nWelcome 👋\nGraphQL server @ http://localhost:${port}/v1\n`,
        )
      })
    }

    createSchema()
    ```

### GraphiQL

## gRPC setup

Todo

## Docker setup

We want to use Docker inside our NodeJS project, so that we can easily deploy our server to other environments without having to go through the process of installing Node and managing the versions.

It's also a good idea to continue to Kubernetes, which will manage our Docker container deployments in a production environment. Ideal for scalability!

Using Visual Studio Code, you can easily configure Docker to your project.

- Press `Ctrl+Shift+P` (or `CMD+Shift+P` on MacOS) to access the `Command Pallette`. Search for `Docker: Add Docker Files to Workspace...` and press that.
  ![Docker setup](images/Docker-setup.png)
- Follow the instructions for `Node.JS`. Usually, you can keep everything on default.
- Also choose to `Include optional Docker Compose files?`. This will give us an easy way to run our Docker container.
- If everything goes well, you should have these 4 new files. You should still remember what these files do, from lessons of Backend development.

  - `.dockerignore`
  - `docker-compose.debug.yml`
  - `docker-compose.yml`
  - `Dockerfile`

- If necessary, change the Docker image name inside `docker-compose.yml` to something else, so you can push it to your own Docker Hub (or GitHub Packages).
