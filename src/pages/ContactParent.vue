<template>
  <div class="layout">
    <el-container>
      <el-header id="topbar">
        <h3>
          <strong>
            Chatting with: {{ receipientSelectedUserFirstName }}
          </strong>
        </h3>
      </el-header>
      <el-container id="sidebarHeader">
        <el-aside width="200px">
          <h3>ChatList</h3>
          <el-table :data="userList" @current-change="selectUser">
            <el-table-column prop="first" label="Parents:"> </el-table-column>
          </el-table>
        </el-aside>

        <el-container id="messageContainer">
          <el-main>
            <ul>
              <Message
                v-for="{ senderName, senderID, message, time } in messageList"
                v-bind:key="time"
                :sender="senderName"
                :senderID="senderID"
                :message="message"
                :time="time"
                :senderId2="$store.state.userModel.teacherID"
              />
            </ul>
          </el-main>
          <el-footer>
            <el-input
              id="message"
              ref="type"
              v-model="message"
              :rows="2"
              v-on:keyup.enter="send()"
              type="textarea"
              placeholder="Type your message here..."
            />
            <el-button
              type="primary"
              @click="send"
              style="
                float: right;
                margin-top: 24px;
                width: 7%;
                margin-right: 0px;
                margin-left: auto;
              "
            >
              Send
            </el-button>
          </el-footer>
        </el-container>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import Message from "../components/Message.vue";
import { db } from "../firebase.js";
import { addDoc, collection, getDocs, where, query } from "firebase/firestore";

export default {
  name: "ContactParent",
  data() {
    return {
      userList: [],
      message: null,
      messageList: [],
      receipientSelectedUserFirstName: "",
      currentSelectedUserFirstName: "",
      senderName: this.$store.state.userModel.first,
      senderID: this.$store.state.userModel.teacherID,
      receiverName: "",
      receiverID: "",
    };
  },
  mounted() {
    this.getUserList();
  },
  components: {
    Message,
  },
  methods: {
    selectUser(user) {
      console.log("user was selected", user);
      var receipientUser = user;
      this.receiverName = receipientUser.first;
      this.receiverID = receipientUser.parentId;
      this.receipientSelectedUserFirstName =
        receipientUser.first + " " + receipientUser.last;
      this.displaychat(user);
    },

    async getUserList() {
      const q = await getDocs(collection(db, "users"));
      q.forEach((doc) => {
        var data = doc.data();
        if (data.type == "parent") {
          this.userList.push(data);
        }
      });
    },

    async send() {
      const msg = {
        message: this.message,
        senderName: this.senderName,
        senderID: this.senderID,
        receiverName: this.receiverName,
        receiverID: this.receiverID,
        time: new Date(),
      };
      this.messageList.push(msg);

      addDoc(collection(db, "messages"), msg)
        .then((response) => {
          console.log(response);
        })
        .catch((err) => {
          console.log(err);
        });
      this.message = "";
    },

    async displaychat(user) {
      console.log("displaycahr called");
      const userSendQuery = query(
        collection(db, "messages"),
        where("senderID", "==", this.senderID),
        where("receiverID", "==", user.parentId),
      );
      const querySend = await getDocs(userSendQuery);

      const userReceiveQuery = query(
        collection(db, "messages"),
        where("senderID", "==", user.parentId),
        where("receiverID", "==", this.senderID),
      );

      const queryReceive = await getDocs(userReceiveQuery);
      var messageList = [];
      querySend.forEach((doc) => {
        messageList.push(doc.data());
        console.log(doc.id, " => ", doc.data());
      });
      queryReceive.forEach((doc) => {
        messageList.push(doc.data());
        console.log(doc.id, " => ", doc.data());
      });
      this.messageList = messageList.sort((a, b) => (a.time > b.time ? 1 : -1));
    },
  },
};
</script>
<style>
.el-footer {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: right;
}
#topbar {
  overflow: hidden;
  background-color: rgb(7, 119, 172);
  display: flex;
  flex-direction: row;
  justify-content: center;

  padding: 5px;
  width: 100%;
  color: white;
}

#btn {
  float: left;
  width: 10%;
  color: blue;
  text-align: center;
  padding: 10px 10px;
  text-decoration: none;
}

#sidebarHeader {
  margin-top: 24px;
}
#messageContainer {
  height: 75vh;
}
h3 {
  margin-left: 8px;
}
</style>
