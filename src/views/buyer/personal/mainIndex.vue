<template>
    <div class="m-mainIndex"  @touchmove="touchMove">
      <div class="m-mainIndex-head m-flex-around" v-if="person_info">
        <div class="m-left">
          <img :src="person_info.usheader"  class="m-avator" alt="">
          <p class="m-user-name">{{person_info.usname}}</p>
          <p class="m-user-level">{{person_info.uslevel_zh}}</p>
        </div>
        <ul class="m-head-num-ul">
          <li @click="changeRoute('/personal/followUser',0)">
            <p class="m-num">{{person_info.follow}}</p>
            <p>关注</p>
          </li>
          <li class="m-num-line"></li>
          <li @click="changeRoute('/personal/followUser',1)">
            <p class="m-num">{{person_info.fens_count}}</p>
            <p>粉丝</p>
          </li>
          <li class="m-num-line"></li>
          <li @click="changeRoute('/collect')">
            <p class="m-num">{{person_info.collected}}</p>
            <p>喜欢</p>
          </li>
        </ul>
      </div>
      <div class="m-mainIndex-edit m-flex-between">
        <div class="m-flex-start" @click="myClick">
          <img src="/static/images/circle/icon-edit.png" class="m-edit-icon" alt="">
          <span>我发布的</span>
        </div>
        <div class="m-position-relative" @click="labelShow = !labelShow">
          <span class="m-label">{{select_nav.itname}}</span>
          <img src="/static/images/circle/icon-up-more.png" class="m-up-more" alt="">
          <div class="m-scroll" v-show="labelShow">
            <ul class="m-label-ul">
              <li :class="select_nav.itid == item.itid ? 'active':''" v-for="(item,index) in nav_list" @click.stop="navClick(index)">
                {{item.itname}}
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div class="m-mainIndex-content">
        <m-circle :index="index" v-for="(item,index) in news_list" v-if="news_list.length > 0"  :is-my="true" @deleteCircle="deleteCircle" :key="index" :circle="item" @followClick="followClick" @likeClick="likeClick" @clickCollect="clickCollect"></m-circle>
        <p class="m-no-data" v-else>该标签下暂无发布的内容</p>
        <bottom-line v-if="bottom_show"></bottom-line>

      </div>
    </div>
</template>

<script>
  import common from '../../../common/js/common';
  import mCircle from '../../../components/common/circle';
  import bottomLine from '../../../components/common/bottomLine';
  import api from '../../../api/api';
  import { Toast,MessageBox } from 'mint-ui';
    export default {
        name: "mainIndex",
      data(){
          return{
            labelShow:false,
            news_list: null,
            page_info: {
              page_num: 1,
              page_size: 5
            },
            isScroll: true,
            total_count: 0,
            bottom_show: false,
            person_info:null,
            nav_list:[
              {
                itdesc: "我是描述",
                itid: "",
                itname: "全部",
                itrecommend: true,
                itsort: null,
                ittype: 10,
                active: true,
                psid: ""
              }
            ],
            select_nav:{}
          }
      },
      components:{
        mCircle,
        bottomLine
      },
      //离开时记录位置
      beforeRouteLeave (to, from, next) {
        if(to.path == '/circle/detail'){
          // sessionStorage.setItem('scrollTop',document.documentElement.scrollTop || document.body.scrollTop)
          this.$store.state.scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
          this.$store.state.all_data = this._data;
          this.$store.state.isChange = true;
        }else{
          this.$store.state.scrollTop = 0;
          this.$store.state.isChange = false;
        }
        next();
      },
      mounted(){
        //  判断是否需要记住浏览位置
        if(this.$store.state.scrollTop >0 ||this.$store.state.isChange  ){
          for(let a in this.$store.state.all_data){
            this._data[a] = this.$store.state.all_data[a]
          }
          document.documentElement.scrollTop =this.$store.state.scrollTop;
        }else{
          this.getInfo();
          this.getNav();
        }
      },
      methods:{
        //  路由改变
        changeRoute(v,param){
          this.$router.push({path:v,query:{index:param}})
        },
        //导航点击
        navClick(index){
          this.page_info.page_num = 1;
          this.bottom_show = false;
          if(this.nav_list[index].itname == this.select_nav.itname){
            return false;
          }
          this.select_nav = this.nav_list[index];
          this.getNews();
          this.labelShow = false;

        },
        //获取个人信息
        getInfo(){
          this.$http.get(api.get_home_top,{
            params:{
              token: localStorage.getItem('token')
            }
          }).then(res => {
            if(res.data.status == 200){
              this.person_info = res.data.data;
            }
          })
        },
        //我发布的点击
        myClick(){
          this.page_info.page_num = 1;
          this.getNews('mynews');
          this.select_nav = {}
        },
        /*获取导航*/
        getNav(){
          this.$http.get(this.$api.items_list, { params: { ittype:10,token:localStorage.getItem('token'),option:2}}).then(res => {
            if(res.data.status == 200){
              if(res.data.data.length == 0){
                this.nav_list = this.nav_list.concat([])
              }else{
                let arr = this.nav_list.concat(res.data.data);
                for(let i=0;i<arr.length;i++){
                  arr[i].active = false;
                }
               arr[0].active = true;
                this.nav_list = [].concat(arr);
              }
              this.select_nav = this.nav_list[0];
              this.getNews();
            }
          })
        },
        /*获取资讯列表*/
        getNews(itid) {
          this.$http.get(api.get_all_news,{
            params:{
              token:localStorage.getItem('token'),
              page_num:this.page_info.page_num,
              page_size: this.page_info.page_size,
              itid: itid || this.select_nav.itid,
              nestatus:'usual',
              homepage:true
            }
          }).then(res => {
            if(res.data.status == 200){
              this.isScroll =true;
              if(res.data.data.length >0){
                for(let i in res.data.data){
                  if(res.data.data[i].netext)
                    res.data.data[i].netext = res.data.data[i].netext.replace(/\r\n/g, '<br/>').replace(/\n/g, '<br/>').replace(/\s/g, '&nbsp;')
                }
                if(this.page_info.page_num >1){
                  this.news_list =  this.news_list.concat(res.data.data);
                }else{
                  this.news_list = res.data.data;
                }
                this.page_info.page_num = this.page_info.page_num + 1;
                this.total_count = res.data.total_count;
              }else{
                this.news_list = null;
                this.page_info.page_num = 1;
                this.total_count = 0;
              }
            }
          })
        },
        //滚动加载更多
        touchMove(e){
          let scrollTop = common.getScrollTop();
          let scrollHeight = common.getScrollHeight();
          let ClientHeight = common.getClientHeight();
          if (scrollTop + ClientHeight  >= scrollHeight -10) {
            if(this.isScroll){
              this.isScroll = false;
              if(this.news_list.length == this.total_count){
                this.bottom_show = true;
              }else{
                if(this.select_nav.itid){
                  this.getNews();
                }else{
                  this.getNews( 'mynews');
                }

              }
            }
          }
        },
        /*点赞*/
        likeClick(i){
          // if(!localStorage.getItem('token')){
          //   Toast('请登录后再试');
          //   let url = location.href.split('#')[0] + '?neid=' + this.news_list[i].neid
          //   localStorage.setItem('login_to',url);
          //   this.$router.push('/login');
          //   return false;
          // }
          this.$http.post(api.favorite_news + '?token='+localStorage.getItem('token'),{
            neid:this.news_list[i].neid,
            tftype:1
          }).then(res => {
            if(res.data.status == 200){
              let arr = [].concat(this.news_list);
              if(arr[i].is_favorite){
                arr[i].favoritnumber = arr[i].favoritnumber-1;
              }else{
                arr[i].favoritnumber = arr[i].favoritnumber+1;
              }
              arr[i].is_favorite = !arr[i].is_favorite;
              this.news_list = [].concat(arr);
            }
          })
        },
        //  收藏
        clickCollect(index){
          this.$http.post(api.collection_collect+'?token=' +localStorage.getItem('token'),{
            uclcollection:this.news_list[index].neid,
            uclcotype:1
          }).then(res => {
            if(res.data.status == 200){
              Toast(
                {
                  message: res.data.message,
                  duration: 500
                });
              let arr = [].concat(this.news_list)
              arr[index].collected = !arr[index].collected;
              // arr.splice(index,1);
              this.news_list = [].concat(arr)
            }
          })
        },
        //  关注
        followClick(index){
          this.$http.post(api.collection_collect+'?token=' +localStorage.getItem('token'),{
            uclcollection:this.news_list[index].neid,
            uclcotype:2
          }).then(res => {
            if(res.data.status == 200){
              Toast(
                {
                  message: res.data.message,
                  duration: 500
                });
              let arr = [].concat(this.news_list)
              //判断当前列表中这个用户，同时改变状态
              for(let i in arr){
                if(arr[i].author.usname == this.news_list[index].author.usname){
                  arr[i].follow = !arr[i].follow;
                }
              }
              this.news_list = [].concat(arr)
            }
          })
        },
        // 删除圈子
        deleteCircle(index) {
          let that = this;
          MessageBox.confirm('你确定要删除这条圈子吗?').then(action => {
            if(action){
              this.$http.post(api.del_news + '?token='+localStorage.getItem('token'),{
                neid: [that.news_list[index].neid]
              }).then(res => {
                if(res.data.status == 200) {
                  Toast('删除成功');
                  // this.$router.go(-1);
                  that.news_list.splice(index,1);
                }
              })
            }
          });
        },
      }
    }
</script>

<style scoped lang="less">
  @import "../../../common/css/index";
  .m-mainIndex{
    color: #000;
    .m-mainIndex-head{
      width: 100%;
      height: 250px;
      background: url("/static/images/circle/icon-mainIndex-bg.png") no-repeat;
      background-size: 100% 100%;
      color: #fff;
      padding: 30px 0;
      /*text-align: left;*/
      .m-avator{
        display: block;
        width: 150px;
        height: 150px;
        border-radius: 50%;
        background-color: #9fd0bf;
      }
      .m-left{
        width: 150px;
        /*margin: 0 0 0 60px;*/
        .m-user-level{
          font-size: 16px;
          border: 1px solid #fff;
          display: block;
          padding: 0 16px;
        }
        .m-user-name{
          font-size: 32px;
          margin: 8px;
        }
      }
      .m-head-num-ul{
        .flex-row(space-between);
        font-size: 20px;
        li{
          /*border-right: 1px solid #fff;*/
          padding: 0 55px;
          &.m-num-line{
            padding: 0;
            height: 40px;
            width: 2px;
            background-color: #fff;
          }
        }
        .m-num{
          font-size: 40px;
        }
      }
    }
    .m-mainIndex-edit{
      padding: 30px 30px 30px 60px;
      border-bottom: 1px solid #F2F2F2;
      font-size: 28px;
      .m-edit-icon{
        display: inline-block;
        width: 30px;
        height: 30px;
        margin-right: 20px;
      }
      .m-label{
        font-size: 24px;
      }
      .m-up-more{
        display: inline-block;
        width: 24px;
        height: 14px;
        margin-left: 10px;
      }
      .m-position-relative{
        position: relative;
        .m-scroll{
          height: 250px;
          overflow: scroll;
          background-color: #fff;
          width: 180px;
          text-align: center;
          position: absolute;
          bottom: -270px;
          right: -30px;
          font-size: 24px;
          transition: height 0.1s ease-out;
          z-index: 10002;
          .m-label-ul{
            overflow-y: scroll;
            overflow-x: hidden;
            li{
              padding: 15px 0;
              &.active{
                color: @mainColor;
              }
            }
          }
        }
      }
    }
    .m-mainIndex-content{
      padding: 0 0;
      background-color: #fff;

    }
  }
  /*滚动条样式*/
  .m-scroll::-webkit-scrollbar { /*滚动条整体样式*/
    width: 0; /*高宽分别对应横竖滚动条的尺寸*/
    height: 0;
    border-radius: 10px;
  }

  .m-scroll::-webkit-scrollbar-thumb { /*滚动条里面小方块*/
    border-radius: 10px;
    -webkit-box-shadow: inset 0 0 5px @mainColor;
    background: @mainColor;
  }

  .m-scroll::-webkit-scrollbar-track { /*滚动条里面轨道*/
    -webkit-box-shadow: inset 0 0 5px #fbea7d;
    border-radius: 10px;
    background: #fbea7d;
  }
</style>
