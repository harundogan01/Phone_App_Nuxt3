<template>
    <v-app>
      <v-navigation-drawer
        v-model="drawer"
        :mini-variant="miniVariant"
        :clipped="clipped"
        fixed
        app
      >
        <v-list>
          <v-list-item
            v-for="(item, i) in items"
            :key="i"
            :to="item.to"
            router
            exact
          >
            <v-list-item-content>
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-navigation-drawer>
      <v-main>
        <v-container>
          <NuxtPage />
        </v-container>
      </v-main>
      <v-footer :absolute="!fixed" app>
        <span>&copy; {{ new Date().getFullYear() }}</span>
      </v-footer>
    </v-app>
  </template>
  
  <script setup>
  const clipped = false
  const drawer = false
  const fixed = false
  const items = [
    {
        title: 'Add New Phones',
        to: '/add'
    },
    {
        title: 'Download All Added Phones',
        to: '/download'
    }
  ]
  const miniVariant = false
  </script>