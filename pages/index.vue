<template>
  <v-container>
    <!--Loading Overlay-->
    <v-overlay :value="overlay">
      <v-progress-circular indeterminate size="64"></v-progress-circular>
    </v-overlay>
    <!-- Search Form -->
    <v-card class="mx-auto mt-5 text-center" max-width="375" outlined light>
      <v-container fluid>
        <v-text-field
          outlined
          label="フリーワード検索"
          hint="場所、店名、ジャンルなどで検索"
          clearable
          color="deep-orange"
          v-model="freeword"
        ></v-text-field>
        <v-btn
          rounded
          color="deep-orange"
          class="white--text"
          width="200"
          @click="frwdSearch"
          x-large
        >
          フリーワード検索
        </v-btn>
      </v-container>

      <v-divider class="my-3"></v-divider>

      <v-btn
        rounded
        color="green"
        class="white--text"
        width="200"
        @click="getCurPos"
        x-large
      >
        現在地から検索
      </v-btn>

      <!-- Search Options -->
      <v-divider class="my-3"></v-divider>

      <v-container>
        <v-expansion-panels>
          <v-expansion-panel>
            <v-expansion-panel-header>オプション</v-expansion-panel-header>
            <v-expansion-panel-content>
              <v-container fluid>
                <v-row v-for="option in options" :key="option.label">
                  <v-col>
                    <v-checkbox
                      :label="option.label"
                      v-model="option.value"
                      false-value="0"
                      true-value="1"
                      value
                    ></v-checkbox>
                  </v-col>
                </v-row>
              </v-container>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-container>
    </v-card>
    <!-- Hit Count -->
    <v-row>
      <v-col>
        <v-alert
          type="success"
          max-width="375px"
          class="mx-auto my-5"
          v-if="shop_data.length !== 0"
        >
          {{ total_hit_count }}件ヒットしました
        </v-alert>
        <!-- Render Shop List-->
        <section>
          <v-card
            max-width="375"
            class="mx-auto mb-10"
            v-for="rest in shop_data"
            :key="rest.id"
          >
            <!-- Shop Image -->
            <div v-if="rest.image_url.shop_image1">
              <v-img :src="rest.image_url.shop_image1" height="200px"></v-img>
            </div>
            <div v-else>
              <v-img
                :src="require('@/assets/noimage.png')"
                height="200px"
                contain
              ></v-img>
            </div>
            <!-- Shop Detail-->
            <v-card-text>
              <div class="text--primary title font-weight-bold">
                <a :href="rest.url" target="_blank">{{ rest.name }}</a>
              </div>
              <div>{{ rest.category }}</div>
              <div class="mt-3">
                <v-icon color="deep-orange"> mdi-google-maps </v-icon
                ><span>住所</span>
                <div>
                  <a :href="rtnMapUrl + rest.address" target="_blank">{{
                    rest.address
                  }}</a>
                </div>
              </div>

              <v-card-actions>
                <v-btn
                  text
                  class="title mx-auto"
                  color="teal accent-4"
                  @click="rest.reveal = true"
                >
                  詳細を表示
                </v-btn>
              </v-card-actions>
              <!-- Detail Section -->
              <v-expand-transition>
                <v-card
                  v-if="rest.reveal"
                  class="transition-fast-in-fast-out v-card--reveal"
                  style="height: 100%"
                  elevation="0"
                >
                  <v-card-text class="pb-0">
                    <v-list two-line>
                      <v-list-item
                        v-for="item in getShopDetailList(rest)"
                        :key="item.id"
                      >
                        <v-list-item-icon>
                          <v-icon color="deep-orange"> {{ item.icon }}</v-icon>
                        </v-list-item-icon>
                        <v-list-item-content>
                          <div>{{ item.text }}</div>
                          <v-list-item-subtitle>{{
                            item.title
                          }}</v-list-item-subtitle>
                        </v-list-item-content>
                      </v-list-item>
                    </v-list>
                  </v-card-text>

                  <v-card-actions class="pt-0">
                    <v-btn
                      text
                      color="teal accent-4"
                      class="mx-auto"
                      @click="rest.reveal = false"
                    >
                      Close
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-expand-transition>
            </v-card-text>
          </v-card>
        </section>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  name: "mainbody",

  components: {},

  data: () => ({
    overlay: false,
    total_hit_count: 0,
    freeword: "",
    api_params: {
      keyid: "17ddc5171d08ed489c205f95fc30e8bc",
      hit_per_page: 100,
      outret: 1,
      wifi: 1,
      mobilephone: 1,
      lunch: 1,
    },
    options: [
      { label: "禁煙席", key: "no_smoking", value: 0 },
      { label: "カード可", key: "card", value: 0 },
      { label: "個室あり", key: "private_room", value: 0 },
      { label: "駐車場あり", key: "parking", value: 0 },
      {
        label: "プロジェクター・スクリーン有り",
        key: "projecter_screen",
        value: 0,
      },
      { label: "電子マネー対応", key: "e_money", value: 0 },
      { label: "朝食あり", key: "breakfast", value: 0 },
    ],
    shop_data: [],
    search_options: [{ label: "" }, {}],
  }),
  computed: {
    rtnMapUrl: function () {
      return "https://www.google.com/maps/search/?api=1&query=";
    },
  },
  methods: {
    getCurPos: function () {
      this.overlay = true;
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          function (position) {
            let coords = position.coords;
            // 緯度経度だけ取得
            this.api_params.latitude = coords.latitude;
            this.api_params.longitude = coords.longitude;
            this.api_params.range = 5; // 3000m radius
            //API問い合わせ
            this.getAPIdata();
            delete this.api_params.latitude;
            delete this.api_params.longitude;
            delete this.api_params.range;
          }.bind(this),
          function (error) {
            // エラー処理を書く
            console.log(error);
          }
        );
      } else {
        console.log("GeoLocationAPIに対応したブラウザをお使いくださいませませ");
      }
    },
    getAPIdata: async function () {
      this.overlay = true;
      console.log(this.options);
      //add options to parameter object.
      this.options.forEach((elem) => {
        this.api_params[elem.key] = parseInt(elem.value);
      });
      // this.api_params = { ...this.api_params};
      console.log(this.api_params);

      let res = await axios.get("https://api.gnavi.co.jp/RestSearchAPI/v3/", {
        params: this.api_params,
      });
      this.total_hit_count = res.data.total_hit_count;
      //カード展開用のフラグ追加
      let temp = res.data.rest;
      this.shop_data = temp.map((val) => {
        val.reveal = false;
        return val;
      });
      this.overlay = false;
    },
    //APIから受け取ったデータを店舗詳細用に加工
    getShopDetailList: function (obj) {
      return [
        { id: 1, title: "電話", text: obj.tel, icon: "mdi-phone" },
        { id: 2, title: "営業時間", text: obj.opentime, icon: "mdi-clock" },
        { id: 3, title: "休業日", text: obj.holiday, icon: "mdi-calendar" },
        {
          id: 4,
          title: "アクセス",
          text: `${obj.access.line} ${obj.access.station} ${obj.access.station_exit} 徒歩${obj.access.walk}分 ${obj.access.note}`,
          icon: "mdi-walk",
        },
        {
          id: 5,
          title: "PR",
          text: obj.pr.pr_short,
          icon: "mdi-chat-processing",
        },
        { id: 6, title: "予算", text: obj.budget, icon: "mdi-cash" },
        {
          id: 7,
          title: "ランチタイム予算",
          text: obj.lunch,
          icon: "mdi-cash",
        },
        {
          id: 8,
          title: "クレジットカード",
          text: obj.credit_card,
          icon: "mdi-card-bulleted-outline",
        },
        {
          id: 9,
          title: "電子マネー",
          text: obj.e_money,
          icon: "mdi-cellphone",
        },
      ];
    },
    frwdSearch: function () {
      this.api_params.freeword = this.freeword;
      this.getAPIdata();
      delete this.api_params.freeword;
    },
  },
  //オプション用の配列を作成
  options_ary: function () {
    return [{}];
  },
  mounted() {
    //console.log(this.getShopDetailList());
  },
};
</script>