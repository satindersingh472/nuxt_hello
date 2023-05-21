<template>
    <v-container fluid class="fill-height" style="display: grid; place-items: center">
      <v-row no-gutters justify="center">
        <v-col>
          <v-card flat>
            <v-card-title v-if="authState !== 'signedin'"
              >You are signed out</v-card-title
            >
            <amplify-authenticator>
              <v-card-text v-if="authState === 'signedin' && user">
                Hello, {{ user.username }}
              </v-card-text>
              <amplify-sign-out  ></amplify-sign-out>
            </amplify-authenticator>
            <v-btn>TODO</v-btn>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
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
  