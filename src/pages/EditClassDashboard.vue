<template>
  <div>
    <div id="topbar">
      <div></div>
      <div id="secondgroup">
        <h1><strong> Manage Dashboard</strong></h1>
      </div>
    </div>
    <br /><br /><br />

    <el-row :gutter="21" justify="space-evenly">
      <el-col :span="6" class="col">
        <el-card class="card">
          <img
            src="https://images.unsplash.com/photo-1586769852836-bc069f19e1b6?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=870&q=80"
            class="image"
          />
          <h5><strong>Search</strong></h5>
          <div class="el-input el-input-group el-input-group--append">
            <el-select
              v-model="searchParams"
              filterable
              remote
              reserve-keyword
              placeholder="Search child ID..."
              :remote-method="remoteMethod"
              :loading="loading"
            >
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
            <div class="el-input-group__append">
              <el-button :icon="Search" @click="searchChild"></el-button>
            </div>
          </div>
          <el-button
            type="primary"
            :icon="ArrowLeft"
            @click="this.$router.push('/GradesDisplayTeacher')"
            >View Class Grades</el-button
          >
        </el-card>
      </el-col>
      <el-col :span="6" class="col">
        <el-card class="card" @click="$router.push('caregiveruploadreport')">
          <img
            src="https://images.unsplash.com/photo-1434030216411-0b793f4b4173?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxzZWFyY2h8MTF8fHVwZGF0ZXxlbnwwfHwwfHw%3D&auto=format&fit=crop&w=800&q=60"
            class="image"
          />
          <div class="cardName">
            <h5><strong> Update Child Report </strong></h5>
          </div>
        </el-card>
      </el-col>
      <el-col :span="6" class="col">
        <el-card class="card" @click="$router.push('caregiveruploadgrade')">
          <img
            src="https://images.unsplash.com/photo-1485546246426-74dc88dec4d9?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxzZWFyY2h8NzZ8fHNjb3JlfGVufDB8fDB8fA%3D%3D&auto=format&fit=crop&w=800&q=60"
            class="image"
          />
          <div class="cardName">
            <h5><strong> Update Child Gradebook </strong></h5>

            <!-- push to update gradebook page -->
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { getAuth } from "firebase/auth";
import { db } from "../firebase.js";
import { collection, getDocs, where, query, orderBy } from "firebase/firestore";
const auth = getAuth();

// import { ref } from 'vue'
// const num = ref(1)
export default {
  name: "EditClassDashboard",
  data() {
    return {
      studentID: "",
      user: auth.currentUser,
      searchParams: "",
      options: [],
      list: [],
      loading: false,
    };
  },

  components: {},

  methods: {
    getOptions() {
      let temp = [];
      getDocs(collection(db, "students")).then((res) => {
        res.forEach((d) => {
          temp.push({ value: d.id, label: d.id });
        });
      });
      this.list = temp;
    },
    searchChild() {
      if (this.searchParams)
        this.$router.push("/dashboard/" + this.searchParams);
    },
    remoteMethod(query) {
      if (query) {
        this.loading = true;
        setTimeout(() => {
          this.loading = false;
          this.options = this.list.filter((item) => {
            return item.value.toLowerCase().includes(query.toLowerCase());
          });
        }, 200);
      } else {
        this.options = [];
      }
    },
  },
  created: function () {
    this.getOptions();
  },
};
</script>
<script setup>
import { Search } from "@element-plus/icons-vue";
</script>

<style scoped>
.el-row {
  height: 600px;
}

.card {
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 60%;
  padding: 20px;
}

img {
  display: block;
  margin-left: auto;
  margin-right: auto;
  height: auto;
  width: 100%;
}
.card:hover {
  box-shadow: rgba(50, 50, 93, 0.25) 0px 50px 100px -20px,
    rgba(0, 0, 0, 0.3) 0px 30px 60px -30px,
    rgba(10, 37, 64, 0.35) 0px -2px 6px 0px inset;
}

#topbar {
  overflow: hidden;
  background-color: whitesmoke;
  display: block;
  margin: 0%;
  padding: 5px;
  width: 100%;
}
#firstgroup {
  float: left;
  width: 10%;
  color: blue;
  text-align: center;
  padding: 10px 10px;
  text-decoration: none;
  font-size: 20px;
  line-height: 80px;
}
#secondgroup {
  float: center;
  width: 100%;
  text-align: center;
  color: Black;
  padding: 20px;
}
.bottom {
  margin-top: 13px;
  line-height: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.button {
  padding: 0;
  min-height: auto;
}
.image {
  width: 100%;
  display: block;
  border-right: none;
  padding-right: 0px;
}
.el-select .el-input.is-focus .el-input__inner {
  border-color: #409eff !important;
}

.el-input.el-input-group.el-input-group--append .el-select .el-input__suffix {
  display: none;
}
</style>