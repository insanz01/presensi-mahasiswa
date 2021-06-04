<template>
  <v-app>

    <v-main class="pt-5">
      <!-- <HelloWorld/> -->
      <transition>
        <keep-alive>
          <router-view/>
        </keep-alive>
      </transition>
    </v-main>

    <v-bottom-navigation
      :value="value"
      color="teal"
      grow
    >
      <v-btn v-for="menu in menus" :key="menu.id" @click="otherPage(menu.route)">
        <span>{{ menu.name }}</span>

        <v-icon>{{ menu.icon }}</v-icon>
      </v-btn>

    </v-bottom-navigation>

  </v-app>
</template>

<script>

export default {
  name: 'App',

  data: () => ({
    value: 1,
    menus: [
      // {id: 1, name: 'Kelas', icon: 'mdi-calendar-text-outline', route: '/kelas'},
      {id: 2, name: 'Home', icon: 'mdi-home', route: '/'},
      // {id: 3, name: 'Akun', icon: 'mdi-account-circle-outline', route: '/akun'},
    ],
  }),
  methods: {
    otherPage (lokasi) {
      // console.log(lokasi)
      this.$router.push(lokasi)
    }
  }
};
</script>
