<template>
  <view>
    <view class="video_tab">
      <view class="video_tab_title">
        <view class="title_inner">
          <uni-segmented-control
            :values="items.map( x => x.title)"
            :current="current"
            @clickItem="onClickItem"
            style-type="text"
            active-color="#d4237a"
          ></uni-segmented-control>
        </view>
        <view class="iconfont iconsearch"></view>
      </view>
      <view class="video_tab_content">
        <view v-if="current<4"><video-main :urlobj="{url:items[current].url,params:items[current].params}"></video-main></view>
        <view v-if="current===4"><video-category></video-category></view>
      </view>
    </view>
  </view>
</template>

<script>
import videoMain from "./video-main/video-main";
import videoCategory from "./video-category/video-category";
export default {
  components: {
    videoMain,
    videoCategory
  },
  data() {
    return {
      items: [
        { title: "推荐",url:"http://157.122.54.189:9088/videoimg/v1/videowp/featured",params:{limit:30,skip:0,order:"hot"} },
        { title: "娱乐",url:"http://157.122.54.189:9088/videoimg/v1/videowp/category/59b25abbe7bce76bc834198a",params:{limit:30,skip:0,order:"new"} },
        { title: "最新",url:"http://157.122.54.189:9088/videoimg/v1/videowp/videowp",params:{limit:30,skip:0,order:"new"} },
        { title: "热门",url:"http://157.122.54.189:9088/videoimg/v1/videowp/videowp",params:{limit:30,skip:0,order:"hot"} },
        { title: "分类",url:"http://157.122.54.189:9088/videoimg/v1/videowp/category",params:{} } 
      ],
      current: 0
    };
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }
    }
  }
};
</script>


<style lang="scss" >
.video_tab{
  .video_tab_title{
    position: relative;
    .title_inner{
      width: 80%;
      margin: 0 auto;
    }
    .iconsearch{
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 3%;
    }
  }
  .video_tab_content{}
}
</style>