<template>
  <div>
    <v-container style="display: grid; place-items: center">
      <v-row align="center" no-gutters justify="space-between">
        <v-text-field
          v-model="description"
          placeholder="Add new todo task"
        ></v-text-field>
        <v-btn class="ma-5" @click="createTodo">Save</v-btn>
      </v-row>

      <h1>All Todos</h1>
      <div v-for="(todo, index) in allTodos" :key="index">
        <!-- <p>{{ todo['name'] }}: {{ todo['description'] }}</p> -->
        <p>{{ todo['name'] }}: {{ todo['description'] }}</p>

      </div>
    </v-container>
  </div>
</template>

<script>
import { API, graphqlOperation } from "aws-amplify";
import * as queries from "@/src/graphql/queries";
import * as mutations from "@/src/graphql/mutations";

export default {
  mounted() {
    this.listAllTodos();
  },
  // Simple query
  methods: {
    async listAllTodos() {
      await API.graphql({ query: queries.listTodos}).then(response =>{this.allTodos = response.data.listTodos.items}).catch(error =>{error});
    },
    async listOneTodo() {
      this.oneTodo = await API.graphql({
        query: queries.getTodo,
        variables: { id: "some id" },
      });
    },
    async createTodo() {
      
      await API.graphql(
        graphqlOperation(mutations.createTodo, { input: {name:this.name,description:this.description} })
      );
    },
  },

  data() {
    return {
      allTodos: undefined,
      oneTodo: undefined,
      todoDetails: undefined,
      name:'Task',
      description: undefined,
    };
  },
};
</script>

<style lang="scss" scoped></style>
