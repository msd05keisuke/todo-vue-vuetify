<template>
  <v-app>
    <v-app-bar app color="purple" dark>
      <v-spacer><v-toolbar-title>RESTful-Vue-Todo</v-toolbar-title></v-spacer>
    </v-app-bar>

    <v-main>
      <todo-list />
    </v-main>
  </v-app>
</template>

<script>
import TodoList from "./components/TodoList.vue";

export default {
  name: "App",

  components: {
    TodoList,
  },

  data: () => ({
    //
  }),
};
</script>
