<template>
  <div id="app">
    <div class="mb30">
      <h3>TenZ流センシ計算サイト</h3>
      <p>TenZ流センシ計算法とは、PC側のマウスDPIを変えずに最適なゲーム内感度を見つける手段です</p>
      <p>彼が昔配信で紹介して話題になった方法となります</p>
    </div>
    <div v-show="beforeStep" class="mb30">
      <hr>
      <h3>まずはマウスDPIを登録します</h3>
      <label for="YourDpi">登録するマウスDPI：</label>
      <input
        type="number"
        name="DPInum"
        v-model="yourDPI"
        @blur="inputDPI"
        min="1"
        max="10000"
        autocomplete="on"
        style="width: 250px"
        placeholder="PC側のマウスDPIを入力してください"
      >
      <p>登録するDPI : {{ yourDPI }}</p>
      <button @click="startDPIcal">基準のDPIを {{ yourDPI }} に設定して自分に合った感度を探す</button>
    </div>
    <div v-show="step" class="mb30">
      <hr>
      <p>設定した基準DPI : {{DPIobject.yourDPI}}</p>
      <h3>ここからは少しずつ自分に合った感度に絞り込んでいきます</h3><br>
      <h2>STEP{{count}}</h2>
      <ul>
        <li style="list-style: none;">まずゲーム内感度を↓のハイ・ローそれぞれの値にしてBOT撃ちをしてください</li>
        <li style="list-style: none;">当てずらいと感じた方と<span style="font-weight: bold">逆のボタン</span>をクリックしてください</li><br>
        <li style="list-style: none;">例）相対ハイセンシの方が視点がブンブンになってしまって当てられない！<br>→相対ローセンシをクリックする</li><br>
        <li style="list-style: none; font-weight: bold">これを気が済むまで繰り返します</li>
      </ul>
      <div style="font-weight: bold; font-size: 20px;" class="mb30">
        <button class="mr20" @click="nextInGameLowSens"><span>相対ローセンシ：{{ DPIobject.baseLowSens }}</span></button>
        <span class="mr20">基準となるセンシ：{{ DPIobject.baseSens }}</span>
        <button class="mr20" @click="nextInGameHighSens"><span>相対ハイセンシ：{{ DPIobject.baseHighSens }}</span></button>
      </div>
      <p>そして、</p>
      <h2>ハイ・ロー、どちらも同じだと感じるところまできたら.....</h2>
      <button @click="registerSens">感度を決定する</button>
    </div>

    <div v-show="yourFinalEDPI">
      <hr>
      <h2>おつかれさまでした！</h2>
      <h3>あなたに最適なゲーム内感度とEDPIは見つかりましたか？</h3>
      <p>あなたのDPI : {{ yourDPI }}</p>
      <p>あなたが最もエイムしやすかったゲーム内感度 : {{ DPIobject.baseSens }}</p>
      <p>あなたのEDPI : {{ finalEDPI }}</p>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        AverageProsEdpi: 280,
        yourDPI: null,
        DPIobject: {},
        options: [],
        beforeStep: true,
        step: false,
        yourFinalEDPI: false,
        count: 0,
        finalEDPI: 0,
      }
    },
    methods: {
      inputDPI(e) {
          this.yourDPI = e.target.value;
      },
      startDPIcal() {
        if (this.yourDPI == 0) {
          alert("有効なDPI数値を入力してください")
          return;
        }
        if (this.yourDPI == null) {
          alert("有効なDPI数値を入力してください")
          return;
        }

        const yourDPI = this.yourDPI; //設定したDPI
        const baseSens = Math.round(this.AverageProsEdpi / yourDPI * 100) / 100; //プロの平均DPIを設定したDPIで割ることで、ゲーム内感度を決める
        const baseHighSens = Math.round(baseSens * 1.5 *100) / 100;
        const baseLowSens = Math.round(baseSens * 0.5 * 100) / 100;

        const DPIobject = {
          yourDPI,
          baseLowSens,
          baseSens,
          baseHighSens
        };
        this.DPIobject = DPIobject;
        console.log("初期オブジェクト");
        console.log(this.DPIobject);
        this.step = true;
        this.beforeStep = false;
        this.count++;
      },
      nextInGameLowSens() {
        const newBaseSens = Math.round(this.DPIobject.baseLowSens * 100) / 100;
        const newLowSens = Math.round(newBaseSens * 0.5 * 100) / 100;
        const newHighSens = Math.round(newBaseSens * 1.5 * 100) / 100;
        this.DPIobject.baseLowSens = newLowSens;
        this.DPIobject.baseSens = newBaseSens;
        this.DPIobject.baseHighSens = newHighSens;
        console.log("ローセンシ更新");
        console.log(this.DPIobject);
        this.count++;
      },
      nextInGameHighSens() {
        const newBaseSens = Math.round(this.DPIobject.baseHighSens * 100) / 100;
        const newLowSens = Math.round(newBaseSens * 0.5 * 100) / 100;
        const newHighSens = Math.round(newBaseSens * 1.5 * 100) / 100;
        this.DPIobject.baseLowSens = newLowSens;
        this.DPIobject.baseSens = newBaseSens;
        this.DPIobject.baseHighSens = newHighSens;
        this.count++;
        console.log("ハイセンシ更新");
        console.log(this.DPIobject);
      },
      registerSens() {
        this.yourFinalEDPI = true;
        this.step = false;
        const finalEDPI = this.yourDPI * this.DPIobject.baseSens;
        this.finalEDPI = finalEDPI;
      },
      resetDPI() {
        this.yourDPI = 0;
      },
    }
  }
</script>

<style>

body {
  height: 1000px;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.mr10 {
  margin-right: 10px;
}
.mr15 {
  margin-right: 15px;
}
.mr20 {
  margin-right: 20px;
}
.mr25 {
  margin-right: 25px;
}
.mr30 {
  margin-right: 30px;
}
.mb10 {
  margin-bottom: 10px;
}
.mb15 {
  margin-bottom: 15px;
}
.mb20 {
  margin-bottom: 20px;
}
.mb25 {
  margin-bottom: 25px;
}
.mb30 {
  margin-bottom: 30px;
}
.pr10 {
  padding-right: 10px;
}
.pr15 {
  padding-right: 15px;
}
.pr20 {
  padding-right: 20px;
}
.pr25 {
  padding-right: 25px;
}
.pr30 {
  padding-right: 30px;
}


</style>
