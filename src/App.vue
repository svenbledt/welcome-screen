<template>
  <div id="app">
    <h1 class="site-title">{{ title }}</h1>
    <span class="site-icon">
      <a target="_blank" href="https://discord.gg/WxFkT6u5BJ"
        ><i class="fa-brands fa-discord"></i
      ></a>
    </span>
    <span class="site-description">{{ currentDate }} - {{ currentTime }}</span
    ><br />
    <pan class="site-subdesc">Server Status: Stabil</pan>

    <ul v-if="entries" class="entry-list">
      <li class="entry-item" v-for="entry in entries" :key="entry.id">
        <span class="entry-daytime"
          >{{ entry[0] }} Uhr, {{ entry[1].replaceAll("/", ".") }}</span
        ><br />
        <h3 class="entry-title">{{ entry[2] }}</h3>
        <span class="entry-description">{{ entry[3] }}</span
        ><br />
      </li>
    </ul>

    <h1 v-else>Keine Events zur Zeit vorhanden!</h1>

    <footer class="footer">
      <img src="./assets/fivem.png" alt="Fivem Logo" />
      <img src="./assets/logo.png" alt="Logo von USG Roleplay" />
      <img src="./assets/partner.png" alt="Partnerlogo noch ausstehend" />
    </footer>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      title: "USG Roleplay Status",
      sheet_id: "10ZkAtQKajKSExdDZgh-cCwhMK9ECInSju1SML7Y_ySs",
      api_token: "AIzaSyAAOCKWgqDv0Br3vCj_7qGutJ8E5z9DbOs",
      ranges: "A2%3AE100",
      entries: [],
      currentDate: "",
      currentTime: "",
    };
  },
  computed: {
    gsheet_url() {
      return `https://sheets.googleapis.com/v4/spreadsheets/${this.sheet_id}/values:batchGet?ranges=${this.ranges}&valueRenderOption=FORMATTED_VALUE&key=${this.api_token}`;
    },
  },
  methods: {
    getData() {
      axios.get(this.gsheet_url).then((response) => {
        this.entries = response.data.valueRanges[0].values.filter(
          (entry) => entry[4] === "X"
        );
      });
    },
    updateCurrentTime() {
      let today = new Date();
      const currentTime = `${today.getHours()}:${
        today.getMinutes() < 10 ? "0" + today.getMinutes() : today.getMinutes()
      }`;
      this.currentTime = currentTime;
    },
    updateCurrentDate() {
      let today = new Date();
      const currentDate = `${
        today.getDate() < 10 ? "0" + today.getDate() : today.getDate()
      }.${
        today.getMonth() + 1 < 10
          ? "0" + (today.getMonth() + 1)
          : today.getMonth() + 1
      }.${today.getFullYear()}`;
      this.currentDate = currentDate;
    },
    refreshData() {
      this.updateCurrentDate();
      this.updateCurrentTime();
      this.getData();
    },
  },
  mounted() {
    this.refreshData();
    setInterval(this.refreshData, 5000);
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");

body {
  background-image: linear-gradient(to top, #860b00 0%, #942424 100%);
}

#app {
  font-family: "Roboto", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #d1d1d1;
  margin: 60px;
}

.site-title {
  font-size: 62px;
  font-weight: 900;
  margin: 80px 0 20px 0;
}

a {
  color: #d1d1d1;
  text-decoration: none;
}

a:hover {
  color: #ffbfab;
}

.site-icon {
  font-size: 62px;
  font-weight: 900;
  color: #7e7e7e;
  margin: 0px 80px 20px 0px;
  float: right;
}

.site-description {
  font-size: 62px;
  color: #9aa7b1;
  font-weight: 500;
  margin: 0;
}

.site-subdesc {
  font-size: 42px;
  color: #9aa7b1;
  font-weight: 500;
  margin: 0;
}

.entry-list {
  padding-left: 0;
}

.entry-item {
  padding: 35px 40px;
  margin: 40px 0;
  background-image: linear-gradient(
    to right,
    #4b4b4b04 0%,
    #1d1d1d67 60%,
    #1d1d1d02 100%
  );
  font-size: 28px;
  line-height: 1.3;
  list-style: none;
}

.entry-daytime {
  color: #0706067e;
  font-weight: 900;
}

.entry-title {
  font-size: inherit;
  margin: 0;
  color: #ffbfab;
  font-weight: 900;
}

.entry-description {
  color: #ffbfab;
}

.footer {
  display: flex;
  justify-content: space-between;
  box-sizing: border-box;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 40px;
  background-image: linear-gradient(to left, #4b4b4b10 0%, #1d1d1d91 100%);
}

.footer img {
  height: 50px;
}
</style>
