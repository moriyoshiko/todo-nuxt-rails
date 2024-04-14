<template>
    <v-form>
      <v-container class="bg-color">
        <v-row>
          <v-col cols="12" md="5">
            <v-text-field v-model="title" counter="20" label="todo" required></v-text-field>
            <p class="errormsg">{{ errorMsg }}</p>
          </v-col>
          <v-col cols="12" md="4">
            <v-btn @click="handleSubmit" color="primary" >作成</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
  </template>
  
  <script>
  export default {
    data() {
      return {
        title: "",
        errorMsg: "",
      };
    },
    computed: {
      user() {
        return this.$store.state.auth.currentUser;
      },
    },
    methods: {
      handleSubmit() {
        if (!this.title) {
          this.errorMsg = "タイトルが空欄です。";
          return;
        } 
        else if (this.title.length > 20) {
          this.errorMsg = "タイトルは1文字以上20文字以下にしてください。";
          return;
        }
        const todo = {
          title: this.title,
          user_id: this.user.id,
        };
        this.$emit("submit", todo); 
        this.title = "";
        this.errorMsg = "";
      }
    }
  };
  </script>
  
  <style>
  .bg-color {
    background-color: cadetblue;
  }
  </style>
  
  