# Adv. fullstack dev. docs

In deze repo kan je alle development files en documentatie vinden voor tijdens de labo's.

## Project setup

### TLDR; Checklist bij een nieuw project

- [ ] Installeer een project: `npm init vite@latest app-name -- --template vue-ts`.
- [ ] `.nvmrc`-file is aangemaakt.
- [ ] `.prettierrc` of `.editorconfig` is aanwezig voor een consistente codebase voor verschillende developers. Kies zelf wat je het handigste vindt.

### Node.js

Gebruik als je kan steeds **nvm**. Maak dan ook een `.nvmrc`-file aan in de root van je project zodat het command **`nvm use`** de node-versie kan vinden.

### Create a vue-app

We gebruiken vite om het project te maken en om een dev-server op te zetten.

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
