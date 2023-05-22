<template>
  <div>
    <div>
        <h1 v-for="(todo,index) in alltodos" :key="index" >All Todos</h1>

    </div>
  </div>
</template>

<script>
import { API } from "aws-amplify";
import * as queries from "./graphql/queries";

export default {
  // Simple query
  methods: {
    async listAllTodos() {
      this.allTodos = await API.graphql({ query: queries.listTodos });
    },
    async listOneTodo() {
      this.oneTodo = await API.graphql({
        query: queries.getTodo,
        variables: { id: "some id" },
      });
    },
  },

  data() {
    return {
      allTodos: undefined,
      oneTodo: undefined,
    };
  },
};
</script>

<style lang="scss" scoped></style>
