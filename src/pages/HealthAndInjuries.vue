<template>
  <div id="header">
    <el-button
      type="primary"
      :icon="ArrowLeft"
      @click="this.$router.go(-1)"
      style="float: left"
      id="back"
      >Back</el-button
    >
    <div>
      <h4 id="title">Injuries & Health</h4>
    </div>
  </div>
  <div id="mainContentEmpty" v-if="!this.noReports">
    <div>{{ displaytext }}</div>
  </div>
  <div id="mainContent">
    <el-card
      class="box-card"
      v-for="x in this.Reports"
      :key="x.id"
      style="padding = 0px;"
    >
      <div id="title2">
        <div id="name">
          {{ x.name }}
        </div>
        <div id="time">
          {{ x.date.toDate().toString().slice(4, 21) }}
        </div>
      </div>
      <div id="contentContainer">
        <div id="reportTitle">
          {{ x.title }}
        </div>
        <div id="reportContent">
          {{ x.text }}
        </div>
      </div>

      <br />
    </el-card>
  </div>
</template>

<script>
import { db } from "../firebase.js";
import { collection, getDocs, query, where, orderBy } from "firebase/firestore";
export default {
  data() {
    return {
      type: this.$store.state.userModel.type,
      Reports: [],

      boo: false,
      displayName: "",
      displaytext: "No Reports at the moment",
      name:
        this.$store.state.userModel.first +
        " " +
        this.$store.state.userModel.last,
      childID: "",
      childName: "",
    };
  },
  methods: {
    noReports() {
      return this.Reports.length == 0;
    },

    //search for student id wrt to name of user then get corresponding report
    async getInfo() {
      if (this.type == "teacher") {
        this.childID = this.$route.params.id;
        const q = query(
          collection(db, "reports"),
          where("childID", "==", this.childID),
          where("category", "==", "Injuries and Health"),
          orderBy("date", "desc"),
        );
        const querySnapshot = await getDocs(q);
        querySnapshot.forEach((doc) => {
          this.Reports.push(doc.data());
        });
      } else {
        const email = this.$store.state.userModel.email;
        const reportsCollection = collection(db, "reports");
        const q = query(
          collection(db, "reports"),
          where("parentEmail", "==", email),
          where("category", "==", "Injuries and Health"),
          orderBy("date", "desc")
        );
        const querySnapshot = await getDocs(q);
        querySnapshot.forEach((doc) => {
          this.Reports.push(doc.data());
        });
      }
      // console.log(this.Reports.length);
    },
  },
  created() {
    this.getInfo();
  },
};
</script>

<style scoped>
#header {
  background-color: rgb(7, 119, 172);
  display: block;
  margin: 0%;
  width: 100%;
}
#title {
  display: inline-block;
  text-align: center;
  vertical-align: middle;
  width: 100%;
  font-family: Arial, Helvetica, sans-serif;
}
#back {
  position: absolute;
  float: left;
  vertical-align: middle;
  padding: 12px;
  margin: 2px;
  margin-left: 30px;
}
#reportContent {
  padding-left: 8px;
  overflow-wrap: break-word;
}
#contentContainer {
  padding: 8px;
}
#reportTitle {
  color: #055981;
  font-weight: 600;
}
#title2 {
  display: flex;
  flex-direction: row;
  width: 100%;
  align-items: center;
  padding: 8px;
  padding-bottom: 16px;
  font-size: 1em;
  background-color: rgb(135, 206, 250, 0.3);
  border-bottom: 0.5px solid #0777ac;
  backdrop-filter: blur(5px);
  -webkit-backdrop-filter: blur(5px);
}

#name {
  font-weight: 600;
}

#time {
  margin-right: 0px;
  margin-left: auto;
  color: grey;
}

#thirdgroup {
  flex: 1;
}

#firstGroup {
  padding-left: 16px;
  flex: 1;
  font-size: 20px;

  color: white;
  background-color: none;
}
#secondgroup {
  flex: 3;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  color: white;
  padding: 10px;
  line-height: 0px;
  font-size: 40px;
}
img#icon {
  height: 50px;
  width: auto;
  filter: invert(100%) sepia(0%) saturate(4349%) hue-rotate(210deg)
    brightness(113%) contrast(101%);
}

.el-card {
  border-radius: 8px;
  margin: 12px 16px;
  --el-card-padding: 0px;
}

@media screen and (max-width: 1000px) {
  img#icon {
    height: 4vw;
    width: auto;
  }
  #firstGroup {
    font-size: 2vw;
  }
  #secondgroup {
    font-size: 3vw;
  }
}

#firstGroup:hover {
  cursor: pointer;
  color: black;
}

#title {
  float: middle;
  text-align: center;
  /* padding: 30px; */
}

#btn {
  color: white;
  font-size: 20px;
}

#text {
  text-align: center;
  margin-top: auto;
  margin-bottom: auto;
}

ul {
  list-style-type: none;
}

ul li {
  margin-bottom: 10px;
}
#time {
  text-align: right;
}

#space {
  width: 10px;
}

#mainContentEmpty {
  height: 70vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

#reportRow {
  display: flex;
  flex-direction: row;
  align-items: center;
}
</style>
