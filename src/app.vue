<template>
<!-- header -->
<!-- header -->
  <div class="view login" v-if="state.username === '' || state.username === null">
    <form class="login-form" @submit.prevent="Login">
      <div class="form-inner">
        <div class="text-3xl text-center text-blue-700"> Kvn-messenger</div>
        <label class="mt-5 italic" for="username">Username</label>
        <input 
        type="text"
        v-model="inputUsername" 
        placeholder="Please enter your username" />
        <input
        type="submit"
        value="Login" />
      </div>
    </form>
  </div>
  
  <div class="view chat" v-else> 
	  <header class="fixed">
		  <button @click="state.username = ''" class="underline logout">Logout</button>
		  <h1 class="text-xl font-bold">Welcome, <strong class="text-yellow-300">{{ state.username }}</strong> </h1>
	  </header>
	  <section class="chat-box">
		  <div
		  v-for="message in state.messages" 
		  :key="message.key"
		  :class="(message.username == state.username ? 'message current-user' : 'message')">
			<div class="message-inner">
				<div class="text-green-500">{{ message.username }}</div>
				<div class="content">{{ message.content }}</div>
			</div>
		  </div>
		  <div v-if="spinner === true" class="flex items-center justify-center">
			  okokok
		  </div>
	  </section>
	  <footer>
		  <form @submit.prevent="SendMessage">
			  <input
			   type="text"
			   v-model="inputMessage"
			   placeholder="Write a message..." />
			  <input
			   type="submit"
			    value="Send" />
		  </form>
	  </footer>
  </div>
</template>

<script>
import { reactive, onUpdated, ref, watch } from 'vue';
import axios from 'axios';
import PulseLoader from 'vue-spinner/src/PulseLoader.vue';

export default {
  setup() {
    const inputUsername = ref("");
	var inputMessage = ref("");
	var spinner = false;

    const state = reactive({
      username: "",
      messages: []
    });

    const Login = () => {
      if (inputUsername.value != "" || inputUsername.value != null) {
        state.username = inputUsername.value;
        inputUsername.value = "";
      }
    }

	onUpdated(() => {
		 console.log(spinner);
	 	if (spinner)
			inputMessage = "";
			spinner = false;
		axios.get("https://firevuechat-a28fa-default-rtdb.firebaseio.com/messages.json")
		.then(response =>  {
			state.messages = response.data;
		})
		.catch(response => {
		console.log("error2", response.data);
		});
	})

	const SendMessage = () => {
		if (inputMessage.value === "" || inputMessage.value === null)
			return;
		axios.post("https://firevuechat-a28fa-default-rtdb.firebaseio.com/messages.json",
		{
			username: state.username,
			content: inputMessage.value
		}).then(response => {
			spinner = true;
			console.log(response.data);
			inputMessage = "";
		}).catch(response => {
			console.log(response);
		});	
	}
		
    return {
		inputUsername,
		state,
		inputMessage,
		spinner,
		Login,
		SendMessage
	}
  },
}
</script>

<style lang="scss">
* {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}
.view {
	display: flex;
	justify-content: center;
	min-height: 100vh;
	background-color: #5d5fdb;
	
	&.login {
		align-items: center;
		.login-form {
			display: block;
			width: 100%;
			padding: 15px;
			
			.form-inner {
				display: block;
				background-color: #FFF;
				padding: 50px 15px;
				border-radius: 16px;
				box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.2);
				h1 {
					color: #AAA;
					font-size: 28px;
					margin-bottom: 30px;
				}
				label {
					display: block;
					margin-bottom: 5px;
					color: #AAA;
					font-size: 16px;
					transition: 0.4s;
				}
				input[type="text"] {
					appearance: none;
					border: none;
					outline: none;
					background: none;
					display: block;
					width: 100%;
					padding: 10px 15px;
					border-radius: 8px;
					margin-bottom: 15px;
					
					color: #333;
					font-size: 18px;
					box-shadow: 0px 0px 0px rgba(0, 0, 0, 0);
					background-color: #F3F3F3;
					transition: 0.4s;
					&::placeholder {
						color: #888;
						transition: 0.4s;
					}
				}
				input[type="submit"] {
					appearance: none;
					border: none;
					outline: none;
					background: none;
					display: block;
					width: 100%;
					padding: 10px 15px;
					background-color: #5d5fdb;
					border-radius: 8px;
					color: #FFF;
					font-size: 18px;
					font-weight: 700;
				}
				&:focus-within {
					label {
						color: #5d5fdb;
					}
					input[type="text"] {
						background-color: #FFF;
						box-shadow: 0 0 6px rgba(0, 0, 0, 0.2);
						&::placeholder {
							color: #666;
						}
					}
				}
			}
		}
	}
	&.chat {
		flex-direction: column;
		header {
			position: relative;
			display: block;
			width: 100%;
			padding: 50px 30px 10px;
			.logout {
				position: absolute;
				top: 15px;
				right: 15px;
				appearance: none;
				border: none;
				outline: none;
				background: none;
				
				color: #FFF;
				font-size: 18px;
				margin-bottom: 10px;
				text-align: right;
			}
			h1 {
				color: #FFF;
			}
		}
		.chat-box {
			border-radius: 24px 24px 0px 0px;
			background-color: #FFF;
			box-shadow: 0px 0px 12px rgba(100, 100, 100, 0.2);
			flex: 1 1 100%;
			padding: 30px;
			.message {
				display: flex;
				margin-bottom: 15px;
				
				.message-inner {
					.username {
						color: #888;
						font-size: 16px;
						margin-bottom: 5px;
						padding-left: 15px;
						padding-right: 15px;
					}
					.content {
						display: inline-block;
						padding: 10px 20px;
						background-color: #F3F3F3;
						border-radius: 999px;
						color: #333;
						font-size: 18px;
						line-height: 1.2em;
						text-align: left;
					}
				}
				&.current-user {
					margin-top: 30px;
					justify-content: flex-end;
					text-align: right;
					.message-inner {
						max-width: 75%;
						.content {
							color: #FFF;
							font-weight: 600;
							background-color: #5d5fdb;
						}
					}
				}
			}
		}
		footer {
			position: sticky;
			bottom: 0px;
			background-color: #FFF;
			padding: 30px;
			box-shadow: 0px 0px 12px rgba(100, 100, 100, 0.2);
			form {
				display: flex;
				input[type="text"] {
					flex: 1 1 100%;
					appearance: none;
					border: none;
					outline: none;
					background: none;
					display: block;
					width: 100%;
					padding: 10px 15px;
					border-radius: 8px 0px 0px 8px;
					
					color: #333;
					font-size: 18px;
					box-shadow: 0px 0px 0px rgba(0, 0, 0, 0);
					background-color: #F3F3F3;
					transition: 0.4s;
					&::placeholder {
						color: #888;
						transition: 0.4s;
					}
				}
				
				input[type="submit"] {
					appearance: none;
					border: none;
					outline: none;
					background: none;
					display: block;
					padding: 10px 15px;
					border-radius: 0px 8px 8px 0px;
					background-color: #5d5fdb;
					color: #FFF;
					font-size: 18px;
					font-weight: 700;
				}
			}
		}
	}
}
</style>