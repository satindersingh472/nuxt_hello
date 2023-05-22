<template>
  <div>
    <v-app>
      <v-app-bar app>
        <v-spacer></v-spacer>
        <v-btn v-if="authState === 'signedin' && user" small class="pa-0" >
          <amplify-sign-out></amplify-sign-out>
        </v-btn>
      </v-app-bar>
      
      <v-main>
        <Nuxt/>
      </v-main>
    </v-app>
  </div>
</template>

<script>
 import { onAuthUIStateChange } from "@aws-amplify/ui-components";
 export default {
    name: "AuthStateApp",
    created() {
      this.unsubscribeAuth = onAuthUIStateChange((authState, authData) => {
        this.authState = authState;
        this.user = authData;
      });
    },
    data() {
      return {
        user: undefined,
        authState: undefined,
        unsubscribeAuth: undefined,
      };
    },
    beforeDestroy() {
      this.unsubscribeAuth();
    },
  };
</script>

<style lang="scss" scoped>
*{
  padding:0px;
  margin: 0px;
}

</style>
