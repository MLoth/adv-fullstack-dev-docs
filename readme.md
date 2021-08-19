# Adv. fullstack dev. docs

In deze repo kan je alle development files en documentatie vinden voor tijdens de labo's.

# Node.js

Gebruik als je kan steeds **nvm**. Maak dan ook een `.nvmrc`-file aan in de root van je project zodat het command `nvm use` de node-versie kan vinden. Zowel de front- als de backend maken we in node.js, dus het is een goed idee om die versies te 'managen'.

# Frontend

## TLDR; Checklist bij een nieuw project

- [ ] Installeer een project: `npm init vite@latest app-name -- --template vue-ts`.
- [ ] `.nvmrc`-file is aangemaakt.
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
npm i         # Of npm install
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

  ```typescript
  npm i vuex --save
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
  npm install -D tailwindcss@latest postcss@latest autoprefixer@latest
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
  - [ ] We gebruiken **typescript** in ons project, inwstalleer `npm install -D typescript` & `npm install -D tslint`.
        De flag `-D` zet het onder `devDependencies` zodat het niet in de build meegenomen wordt.
  - [ ] We also init the typescript config: `npx tsc --init`. We change the config-file to use the dist folder as output: `"outDir": "dist"`.
  - [ ] We also install a dependency to have a smooth dev-server: `npm install -D ts-node`.
  - [ ] We are making this backend using the Express framework: `npm install -S express`.
  - [ ] We will also install the typescript interfaces for Express: `npm install -D @types/express`.
- [ ] Met bovenstaande packages kunnen we de scripts aanvullen in `package.json`:

      "start": "tsc && node dist/app.js",
      "dev": "npx nodemon --watch 'server/**/*' --exec 'ts-node' server/app.ts",

  Let op, bovenstaande scripts gaan er van uit dat de backend in een map server staat.

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
