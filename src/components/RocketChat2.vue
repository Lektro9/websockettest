<template>
  <div>
    <link
      rel="stylesheet"
      href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
      integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
      crossorigin="anonymous"
    >

    <input
      v-model="nickname"
      type="text"
      name="nickname"
    >
    <button
      :disabled="isLoggedIn"
      @click="login"
    >
      einloggen
    </button>
    <button
      :disabled="!isLoggedIn"
      @click="logout"
    >
      disconnect
    </button>

    <div class="col-sm-9 message_section">
      <div class="row">
        <div class="chat_area">
          <ul
            id="messages"
            class="list-unstyled"
          >
            <li
              v-for="(userinfo, index) in chat"
              :key="index"
              class="left clearfix admin_chat"
            >
              <span class="chat-img1 pull-left">
                <img
                  src="images/cat.png"
                  alt="User Avatar"
                  class="img-circle"
                >
              </span>
              <div class="chat-body1 clearfix">
                <strong>{{ userinfo[0] }}</strong>
                <p>{{ userinfo[1] }}</p>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <div class="message_write">
      <input
        v-model="message"
        type="text"
        name="nachricht"
        placeholder="nachricht"
      >
      <button @click="sendMessage()">
        abschicken
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RocketChat2',
  props: {
    msg: {
      type: String,
      default: '',
    },
  },
  data: function () {
    return {
      chat: [['james', 'hi there!'], ['Klaus', 'Ich kann das net!'], ['tobias', 'ich mach mir shcon zuviele gedankne']],
      websocket: null,
      connectedClients: [],
      nickname: '',
      loggedIn: false,
      message: '',
    };
  },
  computed: {
    isLoggedIn: function () {
      // eslint-disable-next-line no-console
      console.log('computed' + this.loggedIn);
      return this.loggedIn;
    },
  },
  created () {
    // eslint-disable-next-line no-console
    console.log(this.loggedIn);
  },
  methods: {
    login () {
      console.log('nickname:' + this.nickname);
      this.websocket = new WebSocket('ws://localhost:5050/chat?client=' + this.nickname);
      // eslint-disable-next-line no-unused-vars
      this.connectedClients = [];
      this.websocket.onopen = () => {
        this.loggedIn = true;
        // eslint-disable-next-line no-console
        console.log(this.loggedIn);
      };
      this.websocket.onclose = function () {
      };
      /* this.websocket.onmessage = function (ev) {
        console.log(ev);
      }; */
      this.websocket.onmessage = (evt) => {
        var event = JSON.parse(evt.data);
        // eslint-disable-next-line no-console
        console.log(evt);
        if (event.type === 'init') {
          this.handleInitEvent(event);
        } else if (event.type === 'clientconnect') {
          this.handleClientConnectEvent(event);
        } else if (event.type === 'clientdisconnect') {
          this.handleClientDisconnectEvent(event);
        } else if (event.type === 'message') {
          this.handleMessageEvent(event);
        }
      };
      this.websocket.onerror = function () {
      };
    },
    logout: function () {
      this.loggedIn = false;
      this.websocket.close();
    },
    handleInitEvent: function (evt) {
      evt.connectedClients.forEach((entry) => {
        this.connectedClients.push(entry);
      });
    },
    handleClientConnectEvent: function (evt) {
      this.connectedClients.push(evt.client);
    },
    handleClientDisconnectEvent: function (evt) {
      var index = this.connectedClients.indexOf(evt.client);
      if (index > -1) {
        this.connectedClients.splice(index, 1);
      }
    },
    handleMessageEvent: function (evt) {
      this.chat.push([evt.client, evt.message]);
    },
    sendMessage () {
      // this.chat.push([this.nickname, this.message]);
      var event = JSON.stringify({ 'type': 'message', 'client': this.nickname, 'message': this.message });
      this.websocket.send(event);
      this.message = '';
    },
  },

};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#custom-search-input {
    background: #e8e6e7 none repeat scroll 0 0;
    margin: 0;
    padding: 10px;
}
#custom-search-input .search-query {
    background: #fff none repeat scroll 0 0 !important;
    border-radius: 4px;
    height: 33px;
    margin-bottom: 0;
    padding-left: 7px;
    padding-right: 7px;
}
#custom-search-input button {
    background: rgba(0, 0, 0, 0) none repeat scroll 0 0;
    border: 0 none;
    border-radius: 3px;
    color: #666666;
    left: auto;
    margin-bottom: 0;
    margin-top: 7px;
    padding: 2px 5px;
    position: absolute;
    right: 0;
    z-index: 9999;
}
.search-query:focus + button {
    z-index: 3;
}
.all_conversation button {
    background: #f5f3f3 none repeat scroll 0 0;
    border: 1px solid #dddddd;
    height: 38px;
    text-align: left;
    width: 100%;
}
.all_conversation i {
    background: #e9e7e8 none repeat scroll 0 0;
    border-radius: 100px;
    color: #636363;
    font-size: 17px;
    height: 30px;
    line-height: 30px;
    text-align: center;
    width: 30px;
}
.all_conversation .caret {
    bottom: 0;
    margin: auto;
    position: absolute;
    right: 15px;
    top: 0;
}
.all_conversation .dropdown-menu {
    background: #f5f3f3 none repeat scroll 0 0;
    border-radius: 0;
    margin-top: 0;
    padding: 0;
    width: 100%;
}
.all_conversation ul li {
    border-bottom: 1px solid #dddddd;
    line-height: normal;
    width: 100%;
}
.all_conversation ul li a:hover {
    background: #dddddd none repeat scroll 0 0;
    color:#333;
}
.all_conversation ul li a {
    color: #333;
    line-height: 30px;
    padding: 3px 20px;
}
.member_list .chat-body {
    margin-left: 47px;
    margin-top: 0;
}
.top_nav {
    overflow: visible;
}
.member_list .contact_sec {
    margin-top: 3px;
}
.member_list li {
    padding: 6px;
}
.member_list ul {
    border: 1px solid #dddddd;
}
.chat-img img {
    height: 34px;
    width: 34px;
}
.member_list li {
    border-bottom: 1px solid #dddddd;
    padding: 6px;
}
.member_list li:last-child {
    border-bottom:none;
}
.member_list {
    height: 380px;
    overflow-x: hidden;
    overflow-y: auto;
}
.sub_menu_ {
    background: #e8e6e7 none repeat scroll 0 0;
    left: 100%;
    max-width: 233px;
    position: absolute;
    width: 100%;
}
.sub_menu_ {
    background: #f5f3f3 none repeat scroll 0 0;
    border: 1px solid rgba(0, 0, 0, 0.15);
    display: none;
    left: 100%;
    margin-left: 0;
    max-width: 233px;
    position: absolute;
    top: 0;
    width: 100%;
}
.all_conversation ul li:hover .sub_menu_ {
    display: block;
}
.new_message_head button {
    background: rgba(0, 0, 0, 0) none repeat scroll 0 0;
    border: medium none;
}
.new_message_head {
    background: #f5f3f3 none repeat scroll 0 0;
    float: left;
    font-size: 13px;
    font-weight: 600;
    padding: 18px 10px;
    width: 100%;
}
.message_section {
    border: 1px solid #dddddd;
}
.chat_area {
    float: left;
    height: 300px;
    overflow-x: hidden;
    overflow-y: auto;
    width: 100%;
}
.chat_area li {
    padding: 14px 14px 0;
}
.chat_area li .chat-img1 img {
    height: 40px;
    width: 40px;
}
.chat_area .chat-body1 {
    margin-left: 50px;
}
.chat-body1 p {
    background: #fbf9fa none repeat scroll 0 0;
    padding: 10px;
}
.chat_area .admin_chat .chat-body1 {
    margin-left: 0;
    margin-right: 50px;
}
.chat_area li:last-child {
    padding-bottom: 10px;
}
.message_write {
    background: #f5f3f3 none repeat scroll 0 0;
    float: left;
    padding: 15px;
    width: 100%;
}

.message_write textarea.form-control {
    height: 70px;
    padding: 10px;
}
.chat_bottom {
    float: left;
    margin-top: 13px;
    width: 100%;
}
.upload_btn {
    color: #777777;
}
.sub_menu_ > li a, .sub_menu_ > li {
    float: left;
    width:100%;
}
.member_list li:hover {
    background: #428bca none repeat scroll 0 0;
    color: #fff;
    cursor:pointer;
}
#connectinfo {
    padding-top: 12px;
    padding-right: 12px;
}

.img-circle {
  border-radius: 50%;
}
</style>
