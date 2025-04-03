## 📝 Exercice 6.1 : Créer une navigation entre pages
### 1️⃣ Installation de Vue Router
```sh
npm install vue-router
```

### 2️⃣ Configuration du routeur (router.js)
```javascript

import { createRouter, createWebHistory } from 'vue-router'
import Home from '@/views/Home.vue'
import About from '@/views/About.vue'
import Profile from '@/views/Profile.vue'

const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  { path: '/profile', component: Profile }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

### 3️⃣ Utilisation de <router-link> et <router-view> (App.vue)
```html
<template>
  <div>
      <nav>
          <router-link to="/">Accueil</router-link> |
      <router-link to="/about">À propos</router-link> |
      <router-link to="/profile">Profil</router-link>
    </nav>
    <router-view />
  </div>
</template>
```

## 📝 Exercice 6.2 : Utiliser des paramètres dynamiques (/user/:id)
### 1️⃣ Ajout de la route dynamique (router.js)
```javascript
import UserProfile from '@/views/UserProfile.vue'

const routes = [
  { path: '/user/:id', component: UserProfile }
]
```

### 2️⃣ Récupération de l’ID (UserProfile.vue)
```html
<script setup>
import { useRoute } from 'vue-router'

const route = useRoute()
</script>

<template>
  <div>
      <h1>Profil de {{ route.params.id }}</h1>
  </div>
</template>
```

## 📝 Exercice 6.3 : Navigation programmée avec $router.push() et $router.replace()
### Ajout des boutons dans Home.vue
```html

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

const goToProfile = () => {
  router.push('/user/Tiburce')
}

const accessAnonymously = () => {
  router.replace('/user/guest')
}
</script>

<template>
  <div>
    <button @click="goToProfile">Voir mon profil</button>
    <button @click="accessAnonymously">Accéder anonymement</button>
  </div>
</template>
```

## 📝 Exercice 6.4 : Routes protégées avec des gardes (beforeEnter, beforeEach)
### 1️⃣ Ajout d’une variable isAuthenticated
```javascript
const isAuthenticated = false
```
### 2️⃣ Protection de la route avec beforeEnter
```javascript
{
  path: '/user/:id',
  component: UserProfile,
  beforeEnter: (to, from, next) => {
    if (!isAuthenticated) {
      next('/login')
    } else {
      next()
    }
  }
}
```
## 📝 Exercice 6.5 : Ajouter une transition entre les pages avec <transition>
### Ajout du composant <transition> dans App.vue
```html
<template>
  <transition name="fade">
    <router-view />
  </transition>
</template>

<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
```

## 📝 Exercice 6.6 : Gérer les erreurs de route avec une page 404
### Ajout de la route NotFound.vue
```javascript
{ path: '/:pathMatch(.*)*', component: NotFound }
```

## 📝 Exercice 6.7 : Ajouter une mise en page dynamique avec des layouts
### 1️⃣ Définition des layouts
```sh
DefaultLayout.vue

AuthLayout.vue
```

### 2️⃣ Chargement dynamique des layouts
```html
<template>
  <component :is="layout">
    <router-view />
  </component>
</template>

<script setup>
import { computed } from 'vue'
import { useRoute } from 'vue-router'
import DefaultLayout from '@/layouts/DefaultLayout.vue'
import AuthLayout from '@/layouts/AuthLayout.vue'

const route = useRoute()

const layout = computed(() => {
  return route.meta.layout === 'auth' ? AuthLayout : DefaultLayout
})
</script>
```

## 📝 Exercice 6.8 : Charger des composants de manière asynchrone (defineAsyncComponent)
```javascript
import { defineAsyncComponent } from 'vue'

const AsyncUserProfile = defineAsyncComponent({
  loader: () => import('@/views/UserProfile.vue'),
  delay: 200,
  timeout: 3000,
  errorComponent: () => 'Erreur de chargement'
})
```

## 📝 Exercice 6.9 : Optimiser les performances avec <keep-alive>
```html

<keep-alive>
  <router-view />
</keep-alive>
```

## 📝 Exercice 6.10 : Ajouter une barre de progression avec vue-progressbar
```sh
npm install vue-progressbar
javascript

import VueProgressBar from '@aacassandra/vue3-progressbar'

app.use(VueProgressBar, { color: 'blue' })
```

## 📝 Exercice 6.11 : Ajouter un store global avec Pinia (useUserStore)
```sh
npm install pinia
```

```javascript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({ user: null }),
  actions: {
    login(userData) {
      this.user = userData
    }
  },
  getters: {
    isLoggedIn: (state) => !!state.user
  }
})
```

## 📝 Exercice 6.12 : Gérer l’authentification avec un store (isAuthenticated)

```javascript
router.beforeEach((to, from, next) => {
  if (to.meta.requiresAuth && !useUserStore().isLoggedIn) {
    next('/login')
  } else {
    next()
  }
})
```

## 📝 Exercice 6.13 : Gérer les rôles des utilisateurs (admin, user)
```javascript
router.beforeEach((to, from, next) => {
  const store = useUserStore()
  if (to.meta.requiresAdmin && store.user?.role !== 'admin') {
    next('/forbidden')
  } else {
    next()
  }
})
```

## 📝 Exercice 6.14 : Persister les données utilisateur dans localStorage

```javascript
import { useUserStore } from '@/stores/user'

const store = useUserStore()
store.user = JSON.parse(localStorage.getItem('user'))
store.$subscribe((_, state) => {
  localStorage.setItem('user', JSON.stringify(state.user))
})
```
## 📝 Exercice 6.16 : Implémenter un système de notifications global avec Pinia

```javascript
export const useNotificationStore = defineStore('notifications', {
  state: () => ({ notifications: [] }),
  actions: {
    addNotification(message, type = 'success') {
      this.notifications.push({ id: Date.now(), message, type })
    }
  }
})
```

```html
<template>
  <div v-for="notif in notifications" :key="notif.id">
    {{ notif.message }}
  </div>
</template>
```