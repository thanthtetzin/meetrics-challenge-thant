### This app is about
- A Vue app that will detect live advertisement element viewability in percentage, clicks on advertiement, active/Inactive user based on page has focus or not.
- Please read full documentation at https://drive.google.com/open?id=1UH098Uif12GohKEZm5z3WThG0NnO3iJz

### About the Project
This project was built by using VueJs framework (VueCli).

Project Folder Structure
```
- public (Where vue files are combined)
- src 
  - assets
  - components
    - HelloWorld.vue (Default file when initialized new vue cli project)
    - Index.vue (This is the component file that has same HTML structure as original index.html from meetric challenge)
    - ViewabilityDetector.vue (This is the component file that implements the modal that detect/shows ads element viewability property values)
  - App.vue (Vue app starts on this file by initializing the components/Index.vue)
  - main.js (Default file from vue-cli create project)
.eslintrc
.gitignore
babel.config.js
package-lock.json
package.json
README.md
```

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
