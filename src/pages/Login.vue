<template>
  <q-page class="flex flex-center">
    <!-- <h1>{{ message }}</h1> -->

    <q-input v-model="num1" />
    <q-input v-model="num2" />

    {{ addition }}

    <!-- <div class="q-pa-md">
      <div class="row">
        <div class="col">
          <q-form>
            <q-input v-model="username" label="Username" />
            <q-input v-model="password" label="Password" type="password" />

            <q-btn color="white" text-color="black" label="Sign In" @click="onLogin()" />

            {{ userpass }}

          </q-form>
        </div>
      </div>
    </div> -->

  </q-page>
</template>

<style>
</style>

<script>

export default {
  name: 'Login',
  computed: {
    addition: function () {
      return this.num1 + this.num2
    }
  },
  data () {
    return {
      username: '',
      password: '',
      num1: 0,
      num2: 0
    }
  },
  methods: {
    onLogin () {
      console.log(this.username, this.password)
    }
  }
}
</script>
