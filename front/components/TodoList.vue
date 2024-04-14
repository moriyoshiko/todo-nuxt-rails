<template>
    <v-card>
      <v-card-title>
        Todo List
        <v-spacer></v-spacer>
        <v-text-field v-model="search" label="Search" single-line hide-details></v-text-field>
      </v-card-title>
      <v-data-table :headers="headers" :items="todos" :search="search" >
        <template v-slot:top>
          <v-dialog v-model="dialog" max-width="500px">
            <v-card>
              <v-card-title>
                <span class="headline">Todo編集</span>
              </v-card-title>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12">
                      <v-text-field v-model="dialogTodo.title" label="タイトル" />
                      <p class="errormsg">{{ errorMsg }}</p>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn @click="close">閉じる</v-btn>
                <v-btn class="primary" @click="updateItem(dialogTodo.id, dialogTodo.title)">更新する</v-btn>
                <v-spacer />
              </v-card-actions>
            </v-card>
          </v-dialog>
        </template>

        <template v-slot:item.action="{ item }">
            <v-icon medium @click="editItem(item)" class="mr-1">mdi-pencil-plus</v-icon>
            <v-icon medium @click="deleteItem(item)">delete</v-icon>
        </template>
      </v-data-table>
    </v-card>
</template>
  
  <script>
  import axios from "@/plugins/axios";

  export default {
    props: ["todos"],
    data() {
      return {
        dialog: false,
        search: "",
        headers: [
          {
            text: "タイトル",
            align: "left",
            sortable: false,
            value: "title"
          },
          { text: "ユーザー名", 
            value: "username"
          },
          {
            text: "Actions",
            value: "action",
          },
        ],
        dialogTodo: {},
        errorMsg: "",
      };
    },
    computed: {
      user() {
        return this.$store.state.auth.currentUser;
      }
    },
    methods: {
      async deleteItem(item) {
        const res  = confirm("本当に削除しますか?");
        if(res){
          await axios.delete(`/v1/todos/${item.id}`);
          const todos = this.user.todos.filter((todo) => {
            return todo.id !== item.id;
          });
          const newUser = {
            ...this.user,
            todos,
          };
          this.$store.commit("auth/setUser", newUser)
        }
      },
      async editItem (item) {
         this.dialogTodo = Object.assign({}, item)
         this.dialog = true;
      },
      async updateItem(id, title) {
        if (!title) {
          this.errorMsg = "タイトルが空欄です。";
          return;
        } 
        // else if (title.length > 20) {
        //   this.errorMsg = "タイトルは1文字以上20文字以下にしてください。";
        //   return;
        // }
        await axios.patch(`/v1/todos/${id}`, {
          todo: {
            title: title
          }
        });
        this.dialog = false;
        this.errorMsg = "";
 
        const todos = this.user.todos.map((todo) => {
              return  {id: todo.id, title: todo.id !== id ? todo.title : title, user_id: todo.user_id, username: todo.username}           
          });
          const newUser = {
            ...this.user,
            todos,
          };
          this.$store.commit("auth/setUser", newUser)
      
      },
      close () {
        this.dialog = false;
        this.dialogTodo = {};
        this.errorMsg = "";
      },
    },
  };
  </script>
  
  <style>
  .errormsg {
    color: red;
  }
  </style>
  