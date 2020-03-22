<template>
  <v-card style="padding:10px;" dark :color="color">
    <v-card-title>{{title}}</v-card-title>
    <v-divider></v-divider>
    <card :tasks="tasks" :status="title"></card>
  </v-card>
</template>

<script>
  import card from '../components/Card'

  export default {
    name: 'Board',

    components: {
      card
    },

    props: ['color', 'title', 'tasks'],

    data: () => ({
      
    }),
  }
</script>
