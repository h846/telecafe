<template>
  <v-container>
    <v-sheet elevation="2" rounded style="padding: 30px">
      <h1>このWebサービスについて</h1>
      <div class="mb-5"></div>
      <dl>
        <dt class="my-2">概要</dt>
        <dd>
          コロナ禍による客足減少で多くの飲食店が大打撃を受けています。<br />
          また企業のテレワーク導入により、リモートで仕事をする場所の需要が爆発的に増えました。<br />
          勤務体制がテレワーク、リモートワークになったが自宅で仕事ができない。気分転換に他の場所でも仕事したいという需要と
          テレワークができる環境を完備している飲食店を結びつけ、両者の理想を叶えるWebサービスを提供します。
        </dd>
        <dt class="mt-3">条件</dt>
        <dd>
          飲食店の掲載条件は以下の通りです。
          <ul>
            <li>電源・Wi-Fi完備</li>
            <li>携帯の電波が入る</li>
            <li>夜だけでなく遅くてもくランチの時間帯も営業をしている</li>
          </ul>
        </dd>
        <dt>運営者</dt>
        <dd>
          Hideki Yashiro<br /><a href="mailto:yassi846@gmail.com"
            >yassi846@gmail.com</a
          >
        </dd>
      </dl>
    </v-sheet>
  </v-container>
</template>
<script>
export default {};
</script>
<style scoped>
dt {
  position: relative;
  width: 60px;
  border-bottom: 3px solid #094;
}

dt:before {
  position: absolute;
  bottom: -3px;
  left: 0;
  width: 20%;
  height: 3px;
  content: "";
  background: #00cc5b;
}
</style>