<template>
    <div id="chat-room">
        <!-- <div v-if="!isRegister" id="register">
            <input type="text" v-model="name" placeholder="请输入昵称~"/>
            <button @click="register">发送</button>
        </div>
        <div v-else id="room">
            <div class="list-container" ref="list">
                <template v-for="item in list">
                    加入群聊信息
                    <div v-if="item.code === 0" class="new">
                        <span>{{item.name}}加入群聊</span>
                    </div>
                    其他人的消息
                    <div v-else-if="item.name !== name" class="others">
                        <div class="other-info">
                            <img src="http://bpic.588ku.com/element_origin_min_pic/01/48/85/92574443926bc96.jpg" />
                            <span>{{item.name}}</span>
                        </div>
                        <div class="other-message">
                            <div>{{item.message}}</div>
                        </div>
                    </div>
                    自己的消息
                    <div v-else class="me">
                        <div class="me-info">
                            <img src="http://bpic.588ku.com/element_origin_min_pic/01/48/85/92574443926bc96.jpg" alt />
                            <span>{{item.name}}</span>
                        </div>
                        <div class="me-message">
                            <div>{{item.message}}</div>
                        </div>
                    </div>
                </template>
            </div>
            <div class="send-message">
                <textarea name id cols="30" rows="10" v-model="message"></textarea>
                <button @click="sendMessage" @keyup.enter="sendMessage">发送消息</button>
            </div>
        </div>-->
        <div v-for="img in imgs">
            <img :src="eval(img.src)" alt />
        </div>
    </div>
</template>

<script>
export default {
    name: "Chat",
    data() {
        return {
            name: null,
            socket: null,
            message: null,
            isRegister: false,
            list: [],
            imgs: [
                {
                    src: "require(['../../static/imgs/pikaqiu.jpg'])"
                },
                {
                    src: "require(['../../static/imgs/pikaqiu.jpg'])"
                },
            ]
        };
    },
    created() {
        this.eval = window.eval
        this.socket = io("http://localhost:8081");
        // 连接成功
        this.socket.on("connectIsOK", data => {
            console.log(data);
        });
        console.log("啦啦啦");
        // 注册并登陆成功
        this.socket.on("registerSuccess", name => {
            console.log(name);
            this.name = name;
        });
        // 注册失败
        this.socket.on("registerFailure", () => {
            this.name = null;
            this.isRegister = false;
            alert("用户名重复，请重新输入...");
        });
        this.socket.on("new", data => {
            this.list.push(data);
        });
        // 接收消息
        this.socket.on("receiveMessage", data => {
            console.log(data);
            this.list.push(data);
            console.log(this.list);
        });
    },
    methods: {
        register() {
            // 发送请求
            if (this.name) {
                this.socket.emit("register", this.name);
                this.name = null;
                this.isRegister = true;
            } else {
                alert("请输入用户名...");
            }
        },
        sendMessage() {
            // 发送消息
            let date = new Date();
            // let time = `${date.getFullYear()}-${date.getMonth() +
            //     1}-${date.getDay()} ${date.getHour()}:${date.getMin}:${date.getSecond()}`;
            this.socket.emit("sendMessage", {
                name: this.name,
                message: this.message
            });
            window.scrollTo(0, this.$refs.list.scrollHeight);
        }
    }
};
</script>

<style scoped>
#chat-room {
    height: 100%;
}
#register,
#room {
    width: 100%;
    height: 100%;
    background: #000;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

#register input {
    margin-right: 10px;
}

.list-container {
    flex: 4;
    width: 100%;
    background: #fafafa;
    align-items: center;
    flex-direction: column;
}

.new {
    display: flex;
    justify-content: center;
}

.new span {
    display: block;
    background: rgba(0, 0, 0, 0.1);
    color: #888;
    padding: 3px 10px;
    border-radius: 20px;
    margin: 20px;
}

.others,
.me {
    display: flex;
    margin: 20px;
}

.me {
    flex-direction: row-reverse;
}

.other-info,
.me-info {
    display: flex;
    flex-direction: column;
}

.other-info {
    margin-right: 30px;
}

.me-info {
    margin-left: 30px;
}

.other-info img,
.me-info img {
    width: 50px;
    border-radius: 50%;
}

.other-info span,
.me-info span {
    text-align: center;
}

.other-message,
.me-message {
    flex: 1;
    position: relative;
}

.other-message {
    margin-right: 80px;
}

.me-message {
    margin-left: 80px;
}

.other-message div,
.me-message div {
    background-color: antiquewhite;
    padding: 20px;
    border-radius: 10px;
}

.me-message div {
    background-color: aquamarine;
}

.other-message div::before {
    content: "";
    display: block;
    width: 20px;
    height: 20px;
    top: 0;
    left: 0;
    position: absolute;
    background-color: antiquewhite;
    transform-origin: 50% 50%;
    transform: translate(-50%, 20px) rotate(45deg);
}

.me-message div::before {
    content: "";
    display: block;
    width: 20px;
    height: 20px;
    top: 0;
    left: 100%;
    position: absolute;
    background-color: aquamarine;
    transform-origin: 50% 50%;
    transform: translate(-50%, 20px) rotate(45deg);
}

.send-message {
    flex: 1;
    width: 100%;
    display: flex;
}

.send-message textarea {
    flex: 1;
}
</style>
