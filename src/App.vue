<template>
  <div id="app">
    <h1 class="site-title">{{ title }}</h1>
    <!--<span class="site-icon">
      <a target="_blank" href="https://discord.gg/WxFkT6u5BJ"
        ><i class="fa-brands fa-discord"></i
      ></a>
    </span>-->
    <span class="site-description">{{ currentDate }} - {{ currentTime }}</span
    ><br />
    <!--<pan class="site-subdesc">Server Status: Stabil</pan>-->

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
      <img
        src="./assets/ZH_SEB.png"
        alt="Stadt Zürich Soziale einrichtungen und Betriebe"
      />
      <img src="./assets/logo.png" alt="Logo von Opportunity" />
      <img
        src="./assets/SAG.png"
        alt="Stiftung SAG - Bessere Chancen für ihre Zukunft"
      />
    </footer>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      title: "Opportunity Welcome",
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
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@500;900&display=swap");

body {
  background: #e8eff4;
}

#app {
  font-family: "Inter", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #323d4a;
  margin: 60px;
}

.site-title {
  font-size: 62px;
  font-weight: 900;
  margin: 80px 0 20px 0;
}
/*
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
*/
.site-description {
  font-size: 62px;
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
  background: #0f05a0;
  font-size: 28px;
  line-height: 1.3;
  list-style: none;
}

.entry-daytime {
  color: #eb5e00;
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
  background: #fff;
}

.footer img {
  height: 50px;
}
@media (max-width: 760px) {
  #app {
    margin: 10px;
  }
  /*Style Code for the Title */
  .site-title {
    font-weight: 600;
    font-size: 40px;
  }

  .site-icon {
    font-size: 32px;
  }
  .site-description {
    font-size: 32px;
  }

  .site-subdesc {
    font-size: 22px;
  }

  /*Style Code for the List */
  .entry-item {
    padding: 18px 20px;
    margin: 20px 0;
    line-height: 0.8;
    font-size: 15px;
  }
  /* Style Code for The Footer */
  .footer {
    justify-content: space-evenly;
    padding: 5px;
  }

  .footer img {
    height: 20px;
  }
}
</style>
