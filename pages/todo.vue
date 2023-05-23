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
      <p v-if="message !== undefined">{{ message }}</p>

      <h1>All Todos</h1>
      <v-row
        style="place-self: start"
        v-for="(todo, index) in allTodos"
        :key="index"
      >
        <!-- <p>{{ todo['name'] }}: {{ todo['description'] }}</p> -->
        <p>
          {{ todo["name"] }}: {{ todo["description"] }}
          <v-btn @click="deleteTodo(todo, $event)" class="ml-5" small
            >Delete</v-btn
          >
        </p>
        <!-- <v-checkbox v-model="todo['id']" :label= "todo['description']"></v-checkbox> -->
      </v-row>
    </v-container>
  </div>
</template>

<script>
import { API, graphqlOperation } from "aws-amplify";
import * as queries from "@/src/graphql/queries";
import * as mutations from "@/src/graphql/mutations";
import * as subscriptions from "@/src/graphql/subscriptions";

export default {
  mounted() {
    this.listAllTodos();
    this.sub_onCreate();
    this.sub_onDelete();
  },
  // Simple query
  methods: {
    async listAllTodos() {
      await API.graphql({ query: queries.listTodos })
        .then((response) => {
          this.allTodos = response.data.listTodos.items;
        })
        .catch((error) => {
          error;
        });
    },
    async createTodo() {
      await API.graphql(
        graphqlOperation(mutations.createTodo, {
          input: { name: this.name, description: this.description },
        })
      );
    },
    sub_onCreate() {
      API.graphql(graphqlOperation(subscriptions.onCreateTodo)).subscribe({
        next: ({ provider, value }) =>
          this.allTodos.push(value["data"]["onCreateTodo"]),
        error: (error) => (this.message = error),
      });
    },
    async deleteTodo(details) {
      API.graphql(
        graphqlOperation(mutations.deleteTodo, { input: { id: details["id"] } })
      );
    },
    sub_onDelete() {
      API.graphql(graphqlOperation(subscriptions.onDeleteTodo)).subscribe({
        next: ({ provider, value }) => {
          const itemToRemove = value["data"]["onDeleteTodo"]["id"];
          this.allTodos = this.allTodos.filter(
            (item) => item.id !== itemToRemove
          );
        },
        error: (error) => console.warn({ error }),
      });
    },
  },

  data() {
    return {
      allTodos: [],
      oneTodo: undefined,
      todoDetails: undefined,
      name: "Task",
      description: undefined,
      message: undefined,
    };
  },
};
</script>

<style lang="scss" scoped></style>
