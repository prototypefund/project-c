<template>
  <div class="container">
    <div class="images">
      <div class="frame">
        <div class="img left" />
      </div>
      <div class="frame">
        <div class="img right" />
      </div>
    </div>

    <h1>{{ $t('welcome.title', {name}) }}</h1>
    <p>{{ $t('welcome.' + flow) }}</p>

    <div class="buttons">
      <button class="primary" @click.prevent="dashboard">{{ $t('welcome.button') }}</button>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Provide, State } from "nuxt-property-decorator";
import { IState } from "@/store";
import { CognitoUser } from "@aws-amplify/auth";
import { Context } from "@nuxt/types";
import {
  RegistrationCompanyQuery,
  RegistrationCompanyQueryVariables,
  RegistrationUserQuery
} from "@/apollo/schema";
import userQuery from "@/apollo/queries/registration/user.gql";
import {ComponentName} from "@/constants/componentName";

@Component({
  name: ComponentName.PagesWelcomeFlow,
  middleware: "authenticated",
  layout: "center",
})
export default class extends Vue {
  name!: string;

  get flow() {
    return this.$route.params.flow || "demand";
  }

  created() {
    this.$track("registration", "welcome:start");
    this.$store.commit("support/context", `zur Begrüßung`);
  }

  dashboard() {
    this.$track("registration", "welcome:dashboard");
    this.$router.push("/dashboard");
  }

  async asyncData(context: Context) {
    let data: Partial<Pick<this, "name">> = {};

    if ((context.store.state as IState).auth.isAuthenticated) {
      const client = context.app.apolloProvider!.defaultClient;
      const result = await client.query<RegistrationUserQuery>({
        query: userQuery,
        fetchPolicy: "network-only"
      });

      if (result.data && result.data.me) {
        const me = result.data.me;
        data.name = me.firstName;
      }
    }

    return data;
  }
}
</script>

<style lang="scss" scoped>
@import "@/assets/colors";
@import "@/assets/scales";

.container {
  display: flex;
  flex-direction: column;

  justify-content: center;
  align-items: center;

  overflow-y: auto;
  padding: 50px;
}

h1 {
  padding-top: 81px;
  padding-bottom: 21px;
}

p {
  color: #7b7b7b;
  max-width: 600px;
  text-align: center;
}

.buttons {
  padding-top: 76px;

  button {
    width: 260px;
    margin-left: 24px;
  }
}

.images {
  display: flex;
  flex-direction: row;
  justify-content: center;

  .frame {
    width: 250px;
    height: 250px;

    border-radius: 50%;
    border: 5px solid $border;

    &:nth-of-type(odd) {
      transform: translateX(20px);
    }

    &:nth-of-type(even) {
      transform: translateX(-20px);
    }
  }

  .img {
    margin: 50px;
    width: 150px;
    height: 150px;

    object-fit: cover;

    -webkit-mask: url(/icons/user.svg) no-repeat center;
    mask: url(/icons/user.svg) no-repeat center;
  }

  .left {
    background-color: $primary;
  }

  .right {
    background-color: $secondary;
  }
}

@media only screen and (max-width: $breakpoint_sm) {
  .buttons {
    text-align: center;

    button {
      margin-top: 12px;
    }
  }
}
</style>
