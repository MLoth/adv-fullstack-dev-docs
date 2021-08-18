# Adv. fullstack dev. docs

In deze repo kan je alle development files en documentatie vinden voor tijdens de labo's.

## Project setup

### TLDR; Checklist bij een nieuw project

- [ ] Installeer een project: `npm init vite@latest app-name -- --template vue-ts`.
- [ ] `.nvmrc`-file is aangemaakt.
- [ ] `.prettierrc` of `.editorconfig` is aanwezig voor een consistente codebase voor verschillende developers. Kies zelf wat je het handigste vindt.
      Onder `code/.prettierrc` vind je een voorbeeld-file met een config die ik handing vind.
- [ ] Plugins nodig? Installeer ze dan (zie plugins).

### Node.js

Gebruik als je kan steeds **nvm**. Maak dan ook een `.nvmrc`-file aan in de root van je project zodat het command `nvm use` de node-versie kan vinden.

### Create a vue-app

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

#### Vue plugins

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
