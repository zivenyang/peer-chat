<script setup lang="ts">
</script>

<template>
  <div>
    <!-- 创建ID -->
    <v-btn @click="handleCreat">Creat</v-btn>
    <span>ID: {{ myId }}</span>

    <!-- 创建连接 -->
    <v-form>
      <v-container>
        <v-row>
          <v-col>
            <v-text-field v-model="friendID" label="Friend ID" required hide-details></v-text-field>
          </v-col>
          <v-col>
            <v-btn @click="handleConnect">Connect</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>

    <!-- 发送消息 -->
    <v-form>
      <v-container>
        <v-row>
          <v-col>
            <v-text-field v-model="message" label="Message" required hide-details></v-text-field>
          </v-col>
          <v-col>
            <v-btn @click="handleSend">Send</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
    <!-- 历史消息 -->
    <v-card>
      <v-card-title>History</v-card-title>
      <v-card-text>
        {{ receive }}
      </v-card-text>
    </v-card>
    <v-btn @click="handleReceive">Receive</v-btn>
  </div>
</template>

<script setup lang="ts">
import { h, ref, watch } from 'vue';
import Peer from 'peerjs';
const myId = ref('');
var peer = ref();
const handleCreat = () => {
  peer.value = new Peer();
  peer.value.on('open', function (id) {
    myId.value = id;
  });
};
const friendID = ref('');
var conn = ref();
const handleConnect = () => {
  conn.value = peer.value.connect(friendID.value);
  conn.value.on('open', function () {
    console.log('Connected to: ' + conn.value.peer);
  });
};
const message = ref('');
var receive = ref([]);

const handleSend = () => {
  conn.value.send(message.value);
  handleReceive();
};

const handleReceive = () => {
  peer.value.on('connection', function (conn) {
    conn.on('data', function (data) {
      console.log('Received', data);
      receive.value.push(data);
    });
  });
};


</script>
