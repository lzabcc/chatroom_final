<template>
	<div id="message" v-scroll-bottom="sessions">
		  <div v-if="currentSession&&currentSession.username!='group_chat'">
		<ul >
			<li v-for="entry in sessions[user.username+'#'+currentSession.username]" >
				<p class="time">
					<span>{{entry.date | time}}</span>
				</p>
				<div class="main" :class="{self:entry.self}">
					  <p class="username">{{entry.fromNickname}}</p>
					<img class="avatar" :src="entry.self ? user.userProfile: currentSession.userProfile" alt="">
					<p v-if="entry.messageTypeId==1" class="text">{{decrypt(entry.content)}}</p>
					  <img v-if="entry.messageTypeId==2" :src="entry.content" class="img">
				</div>
			</li>
		</ul>
	  </div>
		  <div v-else>
		  <ul>
			  <li v-for="entry in sessions['group_chat']" :key="entry.id">
				  <p class="time">
					  <span>{{entry.createTime | time}}</span>
				  </p>
				  <div class="main" :class="{self:entry.fromId==user.id}">
					  <p class="username">{{entry.fromName}}</p>
					  <!-- <img @dblclick="takeAShot" class="avatar" :src="entry.fromId==user.id? user.userProfile:(entry.fromProfile.substring(1, entry.fromProfile.length - 1))" alt=""> -->
					  <img @dblclick="takeAShot" class="avatar" :src="entry.fromId==user.id? user.userProfile:(entry.fromProfile)" alt="">

					  <div v-if="(entry.messageTypeId==1)"><p class="text" v-html="entry.content"></p></div>
					  <div v-else>

						  <el-image :src="entry.content"
											  :preview-src-list="[entry.content]"
											  class="img">
							  <div slot="error" class="image-slot">
								  <i class="el-icon-picture-outline"></i>
							  </div>
						  </el-image>
					  </div>
				  </div>
			  </li>
		  </ul>
		  </div>
	</div>
  </template>
  
  <script>
  import {mapState} from 'vuex'
  import {Decrypt1} from "@/main";
  export default {
	name: 'message',
	data () {
	  return {
		  user:JSON.parse(window.sessionStorage.getItem('user'))
	  }
	},
	computed:mapState([
		'sessions',
		'currentSession',
		  'errorImgUrl'
	]),
	filters:{
		time (date) {
		if (date) {
		  date = new Date(date);
		}

			  let currentDate=new Date();

			  let timeInterval=currentDate.getDate()-date.getDate();


			  let weekdays = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];


			  let timeRange="morning";
			  if (date.getHours()>12){
				  timeRange="afternoon";
			  }

			  if (date.getMonth()==currentDate.getMonth()&&date.getDate()==currentDate.getDate()){
				  return `${timeRange} ${date.getHours()}:${date.getMinutes()}`;
			  }

			  if (date.getFullYear()==currentDate.getFullYear()&&timeInterval<=6&&timeInterval>=1) {

				  if (timeInterval==1){
					  return `yesterday ${timeRange} ${date.getHours()}:${date.getMinutes()}`;
				  }
				  else{
					  return `${weekdays[date.getDay()]} ${timeRange} ${date.getHours()}:${date.getMinutes()}`;
				  }
			  }

			  else
				  return `${date.getFullYear()}-${date.getMonth()+1}-${date.getDate()} ${timeRange}  ${date.getHours()}:${date.getMinutes()}`;
		}
	},
	directives: {

	  'scroll-bottom' (el) {
		//console.log(el.scrollTop);
		setTimeout(function () {
		  el.scrollTop+=99999;
		},1)
	  }
	},
	  methods:{
		  takeAShot(fromName,toName){

			  let s=fromName+""+toName;
  
		  },
		decrypt(content) {
		console.log(content)
		let content1=Decrypt1(content,sessionStorage.getItem("AESKey"),sessionStorage.getItem("AESKey"))
		return content1
		},
	  }
  }
  </script>
  
  <style lang="scss" scoped>
  #message {
	  padding: 15px;
	  height: 60%;
	max-height: 63%;
	overflow-y: scroll;
	  overflow-x: hidden;
	ul {
		list-style-type: none;
		  padding-left: 0px;
		li {
			margin-bottom: 15px;
		}
	}
	.time {
		text-align: center;
		margin: 7px 0;
		> span {
			display: inline-block;
			padding: 0 18px;
			font-size: 12px;
			color: #FFF;
			background-color: #dcdcdc;
			border-radius: 2px;
		}
	}
	.main {
		.avatar {
			float: left;
			margin: 0 10px 0 0;
			border-radius: 3px;
			width: 30px;
			height: 30px;
  
		}
		.text {
			display: inline-block;
			padding: 0 10px;
			max-width: 80%;
			background-color: #fafafa;
		border-radius: 4px;
		line-height: 30px;
		}
		  .img{
			  display: inline-block;
			  height: 100px;
			  width: 100px;
			  margin-top: 15px;
		  }
		  .username{
			  position: relative;
			  left: 35px;
			  top:11px;
			  margin: 0 0;
			  padding: 0 0;
			  border-radius: 4px;
			  line-height: 15px;
			  font-size: 10px;
			  color: grey;
		  }
	}
	.self {
	  text-align: right;
	  .avatar {
		float: right;
		margin: 0 0 0 10px;
		border-radius: 3px;
		width: 30px;
		height: 30px;
	  }
	  .text {
		display: inline-block;
		padding: 0 10px;
		max-width: 80%;
		background-color: #b2e281;
		border-radius: 4px;
		line-height: 30px;
	  }
		  .img{
			  display: inline-block;
			  height: 100px;
			  width: 100px;
			  margin-top: 15px;
		  }
		  .username{
			  //display: inline-block;
			  position: relative;
			  left: 310px;
			  top:11px;
			  margin: 0 0;
			  padding: 0 0;
			  width: 200px;
			  line-height: 15px;
			  font-size: 10px;
			  color: grey;
		  }
	}
  }
  </style>