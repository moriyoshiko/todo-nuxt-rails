<template>
  <div v-if="user">
    <p>名前：{{user.name}}</p>
    <AddTodo @submit="addTodo" />
    <TodoList :todos="user.todos" />
  </div>
</template>

<script>
  import AddTodo from "@/components/AddTodo";
  import TodoList from "@/components/TodoList";
  import axios from "@/plugins/axios";
  import vuetify from "@/plugins/vuetify";

  export default {
    components: {
      AddTodo,
      TodoList,
    },
    data() {
      return {
        todos: [],
      };
    },
    computed: {
      user() {
        return this.$store.state.auth.currentUser;
      }
    },
    methods: {
      async addTodo(todo) {
        const { data } = await axios.post("/v1/todos", { todo});
        this.$store.dispatch("auth/setUser", {
          ...this.user,
          todos: [...this.user.todos, data]
        });
      },
    },
    fetch({
      store,
      redirect
    }) {
      store.watch(
        state => state.auth.currentUser,
        (newUser, oldUser) => {
          if (!newUser) {
            return redirect("/login");
          }
        }
      );
    },
  };

</script>

<style>
</style>

