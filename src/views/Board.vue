<template>
  <div class="board">
    <h2>新增留言</h2>
    <el-card>
      <el-input v-model="createTitle" placeholder="請輸入標題"></el-input>
      <el-input v-model="createContent" placeholder="請輸入留言"></el-input>
      <el-button @click="createMessage">新增留言</el-button>
    </el-card>

    <h2>列表</h2>
    <el-card
      v-for="message in messages"
      :key="message.id"
      style="margin-bottom: 3px"
    >
      <div slot="header">
        <span>{{ message.title }}</span>
        <el-button
          style="float: right; padding: 3px 0"
          type="text"
          @click="openUpdateModal(message)"
          >編輯</el-button
        >
        <el-button
          style="float: right; padding: 3px 0"
          type="text"
          @click="deleteMessage(message.id)"
          >刪除</el-button
        >
      </div>
      <div>{{ message.content }}</div>
    </el-card>

    <el-dialog title="編輯留言" :visible.sync="isModalShow" width="30%">
      <el-input v-model="updateTitle" placeholder="請輸入標題"></el-input>
      <el-input v-model="updateContent" placeholder="請輸入留言"></el-input>
      <span slot="footer" class="dialog-footer">
        <el-button @click="closeUpdateModal">取消</el-button>
        <el-button type="primary" @click="updateMessage">修改</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Board',
  data() {
    return {
      messages: [],
      createTitle: '',
      createContent: '',
      updateId: null,
      updateTitle: '',
      updateContent: '',
    };
  },
  async created() {
    this.readMessages();
  },
  methods: {
    async createMessage() {
      await axios.post('http://localhost:3000/messages', {
        title: this.createTitle,
        content: this.createContent,
      });
      await this.readMessages();
    },
    async readMessages() {
      const result = await axios.get('http://localhost:3000/messages');
      this.messages = result.data;
    },
    async updateMessage() {
      await axios.put('http://localhost:3000/messages/' + this.updateId, {
        title: this.updateTitle,
        content: this.updateContent,
      });
      await this.readMessages();
      this.closeUpdateModal();
    },
    async deleteMessage(id) {
      await axios.delete('http://localhost:3000/messages/' + id);
      await this.readMessages();
    },
    openUpdateModal(message) {
      this.isModalShow = true;
      this.updateId = message.id;
      this.updateTitle = message.title;
      this.updateContent = message.content;
    },
    closeUpdateModal() {
      this.isModalShow = false;
      this.updateId = null;
      this.updateTitle = '';
      this.updateContent = '';
    },
  },
};
</script>

<style scoped lang="scss"></style>
