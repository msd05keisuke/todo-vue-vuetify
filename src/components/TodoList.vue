<template>
  <v-container style="max-width: 600px">
    <v-text-field
      v-model="newTodo"
      label="What are you working on?"
      solo
      @keydown.enter="addTodo"
    >
      <template v-slot:append>
        <v-fade-transition>
          <v-icon v-if="newTodo" @click="addTodo"> add </v-icon>
        </v-fade-transition>
      </template>
    </v-text-field>

    <v-divider class="mt-4"></v-divider>

    <v-row class="my-1" align="center">
      <strong class="mx-4 info--text text--darken-2">
        Remaining: {{ remainingTodos }}
      </strong>

      <v-divider vertical></v-divider>

      <strong class="mx-4 success--text text--darken-2">
        Completed: {{ completedTodos }}
      </strong>

      <v-spacer></v-spacer>

      <v-progress-circular :value="progress" class="mr-2"></v-progress-circular>
    </v-row>

    <v-divider class="mb-4"></v-divider>

    <v-card v-if="todos.length > 0">
      <template v-for="(todo, i) in todos">
        <v-divider v-if="i !== 0" :key="`${i}-divider`"></v-divider>

        <v-list-item :key="`${i}-${todo.title}`">
          <v-list-item-action>
            <v-checkbox
              @change="completedTodo(todo)"
              v-model="todo.completed"
              :color="(todo.completed && 'grey') || 'primary'"
            >
              <template v-slot:label>
                <div
                  :class="(todo.completed && 'grey--text') || 'primary--text'"
                  class="ml-4"
                  v-text="todo.title"
                ></div>
              </template>
            </v-checkbox>
          </v-list-item-action>

          <v-spacer></v-spacer>

          <v-scroll-x-transition>
            <v-icon v-if="todo.completed" color="success"> mdi-check </v-icon>
          </v-scroll-x-transition>
          <v-list-item-action>
            <v-btn icon @click="showDeleteDialog(todo)">
              <v-icon color="grey lighten-1">mdi-delete</v-icon>
            </v-btn>
          </v-list-item-action>
        </v-list-item>
      </template>
    </v-card>

    <!-- modal forの中にいれてはダメ！再帰呼び出しされてエラーになる-->
    <v-dialog v-model="delete_dialog" persistent max-width="290">
      <v-card>
        <v-card-title class="text-h5"> 本当に削除しますか？ </v-card-title>
        <v-card-text>削除したタスクを元に戻すことはできません。</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" text @click="delete_dialog = false">
            戻る
          </v-btn>
          <v-btn
            color="green darken-1"
            text
            @click="deleteTodo(delete_data.id)"
          >
            削除
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
export default {
  name: "todo-list",
  data() {
    return {
      newTodo: "",
      todos: [],
      delete_data: [],
      delete_dialog: false,
    };
  },
  mounted() {
    this.fetchTodos();
  },
  computed: {
    completedTodos() {
      return this.todos.filter((todo) => todo.completed).length;
    },
    progress() {
      return (this.completedTodos / this.todos.length) * 100;
    },
    remainingTodos() {
      return this.todos.length - this.completedTodos;
    },
  },
  methods: {
    showDeleteDialog(p) {
      this.delete_data = p;
      this.delete_dialog = true;
      /* console.log(this.delete_data); */
    },
    fetchTodos() {
      this.axios.get("http://localhost/api/todos").then((res) => {
        this.todos = res.data;
        /* console.log(this.todos); */
      });
    },
    addTodo() {
      if (this.newTodo.length == 0) {
        return;
      }
      this.axios
        .post("http://localhost/api/todos", {
          title: this.newTodo,
        })
        .then(() => {
          this.fetchTodos();
          this.newTodo = "";
        });
    },
    deleteTodo(todo_id) {
      var url = "http://localhost/api/todos/" + todo_id;
      this.axios
        .delete(url, {
          id: todo_id,
        })
        .then(() => {
          this.fetchTodos();
          this.delete_dialog = false;
        });
    },
    completedTodo(todo) {
      var url = "http://localhost/api/todos/" + todo.id;
      this.axios
        .patch(url, {
          completed: todo.completed,
        })
        .then(() => {
          this.fetchTodos();
        });
    },
  },
};
</script>

<style>
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
}
</style>