<template>
    <div>
      <div class="mainTag"><i class="iconfont icon-tag1"></i>{{tagName}}</div>
      <ul class="cq-item-list">
        <transition-group  name="component-fade" mode="out-in">
        <router-link class="clearFix" :to="'/Detail/'+item.artId" :key="item.artId" tag="li" v-for="item in articleList">
          <div class="cq-leftImg">
            <img :src="item.artThumb" alt="item.artTitle"/>
          </div>
          <div class="cq-right-content">
            <h4 class="item-title">{{item.artTitle}}</h4>
            <div class="item-abstract">{{item.artAbstract}}</div>
            <div class="item-meta">
              <span><i class="iconfont icon-shijian1"></i>{{item.artCdate}}</span><span><i class="iconfont icon-yuedu"></i>{{item.readNum}}</span><span><i class="iconfont icon-xiaoxi"></i>0</span><span><i class="iconfont icon-fenlei"></i>{{item.artType}}</span>
            </div>
          </div>
        </router-link>
        </transition-group>
      </ul>
      <Pages v-if="pageShow" :pageNo="pageNo" :current="currentPage" @currentPages="currentPages"></Pages>
    </div>

</template>

<script>
  import {getArtListByTagId} from '../api/api'
  import Pages from './paginations'
    export default {
      components: {Pages},
      data() {
          return {
            articleList: [],
            tagName:'',
            currentPage: 1,
            pageNo: 1,
            pageRow:7,
            pageShow:false
          }
      },
      methods: {
          getArtList(){
            this.$http.post(getArtListByTagId,{tagId: this.$route.params.id, pageNum: this.currentPage, pageRow: this.pageRow }).then((res) => {
              // success
              this.articleList = res.data.article;
              this.tagName = res.data.article[0].tagName;
              if(res.data.total == 0){
                this.pageNo == 1
              }else {
                this.pageNo = Math.ceil(res.data.total / this.pageRow);
              }
              this.pageShow = true
            }, (error) => {
              // error
              console.log(error)
            });
          },
        currentPages(data){
          this.currentPage = data;
        },
        resetPage(){
          this.pageShow = false;
          this.currentPage = 1;
          this.getArtList();
        }
      },
     created(){
       this.getArtList();
     },
      watch:{
        '$route':'resetPage',
        currentPage: 'getArtList',
      }
    }
</script>

<style scoped>
  ol, ul {list-style: none;}
  .mainTag {background: rgba(255,255,255,1);height: 141px;text-align: center;font-size: 40px;text-transform : capitalize;line-height: 141px;font-style: italic;color: #555;margin-bottom: 15px;}
  .mainTag i{font-size: 35px;margin-right: 10px;}
  img {height: 100%;width: 100%;}
  .cq-item-list li {padding: 10px;background: rgba(255,255,255,1);border-radius: 5px;margin-bottom: 15px;cursor: pointer;transition: all 0.4s ease-in-out 0s;}
  .cq-leftImg, .cq-right-content {float: left;}
  .cq-leftImg {
    height: 119px;
    width: 175px;
  }
  .cq-right-content {padding-left: 10px;width: 64%;}
  .item-title {
    color: #444;
    font-size: 16px;
    padding: 0 0 5px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .item-abstract {height: 76px;margin-bottom: 5px;color: #666;font-size: 13px;}
  .item-meta {font-size: 12px;color: #888;}
  .item-meta i {font-size: 12px;margin-right: 4px;}
  .item-meta span {margin-right: 15px;}
  .component-fade-enter-active, .component-fade-leave-active {
    transition: opacity .5s ease;
  }
  .component-fade-enter, .component-fade-leave-to {
    opacity: 0;
  }
</style>
