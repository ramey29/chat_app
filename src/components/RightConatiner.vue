<template>
<div class="rightConatiner">
    <div v-if="nochat" class="firstTimeUser"> <img src="../assets/img/startChatting.png" /></div>
    <div class="chatConatiner" v-else>
        <RightConatinerHeader></RightConatinerHeader>
        <div class="chatMainDV clearfix">
          <div class="fLft" v-bind:id="currentID">
                <div class="talk-bubble tri-right round right-in"  v-for="(message, index) in messages" :key="index">
                    <div class="talktext"><p>{{message}}</p></div>
                </div>
        </div>

        </div>
        <footer class="footerChat">
            <span class="footerIcon"><img src="../assets/img/smile.png" /></span>
            <input id="chatbox" v-model="chatinput" type="text" placeholder="Type a message" @keyup.enter="chatTypeEvent" /> 
            <span class="footerIcon"><img src="../assets/img/microphone.png" /></span>
        </footer>
    </div>
</div>



</template>

<script>
import { debounce } from './../common/Utils';
import * as Constant from './../common/Constants';
import GlobalStorage from './../common/GlobalStorage';
import { subscribeEvent } from './../common/Observer';
import RightConatinerHeader from "./RightConatinerHeader";



export default {
   
    data() {
        return {
            nochat:false,
            chatinput:'',
            messages : [],
            currentID:0
        }
    },
     components: {
         RightConatinerHeader
    },
    created() {
        subscribeEvent('idSelected',(data)=>{
            this.currentID = data;
        });
        
    },
    mounted(){
    },
    sockets: {
        recieveMessage(data) {
            console.log('data', data);
            this.messages.push(data);
        }
    },
    methods: { 
   
    chatTypeEvent : function() {
                   if(this.chatinput !== ''){
                    this.$socket.emit('message', {id: GlobalStorage.get('selectedId'), message: this.chatinput})
                    this.addChatMessage(this.chatinput);
                    this.chatinput = '';
                   }
    },  
    addChatMessage : function(data, options) {
            let x = this.messages;
            x.push(data);
            this.messages = x;
           this.scrollToEnd();
    },
     scrollToEnd: function() {    	
      let container = this.$el.querySelector(".chatMainDV");
      container.scrollTop = container.scrollHeight;
    },


    
    },
    computed:{
        
    }
}
</script>

<style lang="scss" scoped>
.rightConatiner {
    background-color: #f7f9fa;
    cursor: default;border-left:1px solid #e0e0e0;
    height:100vh;position: relative;
    .firstTimeUser{text-align:center; position: absolute;top: 50%;left: 50%;transform: translate(-50%, -50%);
                    img{width:600px}
                }
    .chatMainDV{
        position: relative;
        top: 0; bottom: 0; left: 0; right: 0;
        overflow-y: scroll;height:calc(100vh - 134px);
            .talk-bubble {margin: 10px 20px;display: block;position: relative;max-width: 400px;height: auto;background-color: #fff;}
            .round{border-radius: 20px;-webkit-border-radius: 20px;-moz-border-radius: 20px;}
      
            .tri-right.right-in:after{content: ' ';position: absolute;width: 0;height: 0;left: auto;
                left: -12px;top: 10px;bottom: auto; border: 12px solid;border-color: transparent transparent #fff transparent;}
            .talktext{padding: 1em;text-align: left;line-height: 1.5em;font-size:12px;}
            .talktext p{-webkit-margin-before: 0em;-webkit-margin-after: 0em;}
    }
    .footerChat{position:fixed;bottom:0;width:100%;
        background-color: #efefef;
        padding:10px;
        input{width:55%;max-width:55%;height:50px;border:none;border-radius: 0 12px;border-radius:12px;}
        .footerIcon{display:inline-block;margin:0 14px;width:32px;
            img{width:32px;vertical-align: -8px}
        }

    }
   
}
</style>
