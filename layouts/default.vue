<template>
  <v-app id="app">
    <v-app-bar app color="deep-orange">
      <v-app-bar-nav-icon
        @click.stop="showDrawer = !showDrawer"
      ></v-app-bar-nav-icon>
      <v-toolbar-title to="/" id="app-title" class="white--text"
        >テレカフェ
        <div class="caption yellow--text">
          テレワークができるレストラン・カフェ検索
        </div></v-toolbar-title
      >
      <v-spacer></v-spacer>
    </v-app-bar>

    <v-navigation-drawer v-model="showDrawer" fixed temporary>
      <v-list nav dense>
        <v-list-item-group active-class="deep-orange--text text--accent-4">
          <v-list-item to="/">
            <v-list-item-icon>
              <v-icon>mdi-home</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Home</v-list-item-title>
          </v-list-item>
          <v-list-item to="About">
            <v-list-item-icon>
              <v-icon>mdi-information</v-icon>
            </v-list-item-icon>
            <v-list-item-title>About</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>

    <v-main>
      <nuxt />
    </v-main>

    <v-footer padless>
      <v-col class="text-right" cols="12">
        Supported by
        <a href="https://api.gnavi.co.jp/api/scope/" target="_blank">
          ぐるなびWebService
        </a>
      </v-col>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "App",

  metaInfo: {
    title: "テレカフェ",
  },

  components: {},

  data() {
    return {
      showDrawer: false,
    };
  },
};
</script>
<style>
@import url("https://fonts.googleapis.com/css2?family=Kosugi&family=Kosugi+Maru&display=swap");
#app {
  font-family: "Kosugi", sans-serif;
  background-image: url("~@/assets/bg.png");
  background-position: center center;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-size: cover;
  background-color: #464646;
}
#app::before {
  content: "";
  position: fixed;
  /* 輪郭がぼやけてしまうのでブラー範囲を広げる */
  top: -5px;
  bottom: -5px;
  left: -5px;
  right: -5px;
  background: inherit;
  filter: blur(3px);
}
#app-title {
  font-family: "Kosugi Maru", sans-serif;
}
</style>
