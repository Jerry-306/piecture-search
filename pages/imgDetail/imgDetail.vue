<template>
	<view>
		<!-- 用户信息 开始 -->
		<view class="user_info">
			<view v-if="imgDetail.user !== undefined" class="user_icon">
				<image :src="imgDetail.user.avatar" mode="widthFix"></image>
			</view>
			<view v-else class="user_icon">
				 <image src="../../static/默认作者头像.jpg" mode="widthFix"></image>
			</view>
			<view class="user_desc">
				<view v-if="imgDetail.user !== undefined" class="user_name">{{imgDetail.user.name}}</view>
				<view v-else class="user_name">润润</view>
				<view class="user_time">{{imgDetail.cnTime}}</view>
			</view>
		</view>
		<!-- 用户信息 结束 -->

		<!-- 高清大图 开始 -->
		<view class="high_img">
			<swiper-action @swiperAction="handleSwiperAction">
				<image
					mode="widthFix"
					:src="imgDetail.thumb"></image>
			</swiper-action>
		</view>
		<!-- 高清大图 结束 -->

		<!-- 点赞 开始 -->
		<view class="user_rank">
			 <view class="rank">
				<text class="iconfont icondianzan">{{imgDetail.rank}}</text>
			 </view>
			 <view class="user_collect">
				<text class="iconfont iconshoucang">收藏</text>
			 </view>
		</view>
		<!-- 点赞 结束 -->

		<!-- 专辑 开始 -->
		<view
			class="album_wrap"
			v-if="album.length">
			<!-- 标题 -->
			<view class="album_title">相关</view>
			<!-- 内容 -->
			<view class="album_list">
				<view
					class="album_item"
					v-for="item in album"
					:key="item.id">
					<!-- 左面 -->
					<view class="album_cover">
						<image
							:src="item.cover"
							mode="aspectFill"></image>
					</view>
					<!-- 右边 -->
					<view class="album_info">
						<view class="album_info_text">专辑</view>
						<view class="album_name">{{item.name}}</view>
						<text class="iconfont iconiconfontjiantou4"></text>
					</view>
				</view>
			  </view>
		</view>
		<!-- 专辑 结束 -->

		<!-- 最热评论 comment hot -->
		<view class="comment hot" v-if="hot.length">
			<view class="comment_title">
				<text class="iconfont iconhot1"></text>
				<text class="comment_text">最热评论</text>
			</view>
			<view class="comment_list">
				<view
					class="comment_item"
					v-for="item in hot"
					:key="item.id">
					<!-- 用户信息 -->
					<view class="comment_user">
						<!-- 用户头像 -->
						<view class="user_icon">
							<image
							mode="widthFix"
							:src="item.user.avatar"></image>
						</view>
						<!-- 用户名称 -->
						<view class="user_name">
							<view class="user_nickname">{{item.user.name}}</view>
							<view class="user_time">{{item.cnTime}}</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user_badge">
							<image
							mode="widthFix"
							:src="item.user.avatar"></image>

							</view>
						</view>
						<!-- 评论数据 -->
						<view class="comment_desc">
							<view class="comment_content">{{item.content}}</view>
							<view class="comment_like">
								<text class="iconfont icondianzan">{{item.size}}</text>
							</view>
						 </view>
				</view>
				</view>
			</view>
			<!-- 最新评论 comment new -->
			<view
				class="comment new"
				v-if="comment.length">
				<view class="comment_title">
					<text class="iconfont iconpinglun"></text>
					<text class="comment_text">最新评论</text>
				</view>
				<view class="comment_list">
					<view
						class="comment_item"
						v-for="item in comment"
						:key="item.id">
						 <!-- 用户信息 -->
						 <view class="comment_user">
							<!-- 用户头像 -->
							<view class="user_icon">
								<image
									mode="widthFix"
									:src="item.user.avatar"></image>
							</view>
							<!-- 用户名称 -->
							<view class="user_name">
								<view class="user_nickname">{{item.user.name}}</view>
								<view class="user_time">{{item.cnTime}}</view>
							</view>
							<!-- 用户徽章 -->
							<view class="user_badge">
								<image
									v-for=" item2 in  item.user.title"
									:key="item2.icon"
									:src="item2.icon" ></image>
							</view>
						</view>
						<!-- 评论数据 -->
						<view class="comment_desc">
							<view class="comment_content">{{item.content}}</view>
							<view class="comment_like">
							<text class="iconfont icondianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>

		<!-- 下载 开始 -->
		<view class="download">
			<view
				class="download_btn"
				@click="handleDownload"
			>下载图片</view>
		</view>
		<!-- 下载 结束 -->
	</view>
</template>

<script>
import swiperAction from "@/components/swiperAction";
import SOtime from '../../js_sdk/fl-SOtime/SOtime.js';

export default {
  components: {
    swiperAction
  },
  data() {
    return {
      // 图片信息对象 包含着用户头像。。
      imgDetail: {},
      // 专辑数据 数组
      album: [],
      // 最热评论
      hot: [],
      // 最新评论
      comment: [],
      // 图片的索引
      imgIndex: 0
    };
  },
  onLoad() {
	    // 对象解构
		const { imgIndex } = getApp().globalData;

		this.imgIndex = imgIndex;
		this.getData();
  },
  methods: {
    // 获取对象数据
    getData() {
      const { imgList } = getApp().globalData;
      this.imgDetail = imgList[this.imgIndex];

      // xxx年前的数据
      this.imgDetail.cnTime = SOtime.time1(this.imgDetail.atime);

      // 获取图片详情的id
      this.getComments(this.imgDetail.id);
    },
	// 获取评论数据
    getComments(id) {
      this.request({
        // url: `http://157.122.54.189:9088/imagev2/wallpaper/wallpaper/${id}/comment`
        url: `http://service.picasso.adesk.com/v2/wallpaper/wallpaper/${id}/comment`
      }).then(result => {

        this.album = result.res.album;

        // 给hot数组中的对象添加一个时间属性 xxx月前
        result.res.hot.forEach(
			v => (v.cnTime = SOtime.time1(v.atime ))
        );
        result.res.comment.forEach(
			v => (v.cnTime = SOtime.time1(v.atime))
        );

        this.hot = result.res.hot;
        this.comment = result.res.comment;
      });
    },
    // 滑动事件
    handleSwiperAction(e) {
      /* 
     1 用户 左滑 imgIndex++  
     2 用户 右滑 imgIndex-- 
     3 判断 数组是否越界问题！！
     4 左滑 e.direction==="left"&&this.imgIndex<imgList.length-1 
     5 右滑 e.direction==="right"&&this.imgIndex>0
      */
      const { imgList } = getApp().globalData;
      if (e.direction === "left" && this.imgIndex < imgList.length - 1) {
        //  可以进行 左滑 加载下一页
        this.imgIndex++;
        this.getData();
      } else if (e.direction === "right" && this.imgIndex > 0) {
        // 右滑 加载上一页
        this.imgIndex--;
        this.getData();
      } else {
        uni.showToast({
          title: "没有数据了",
          icon: "none"
        });
      }
    },
    // 点击下载图片 async await promise
    async handleDownload() {
      // uni.downloadFile
      // uni.saveImageToPhotosAlbum

      await uni.showLoading({
        title:"下载中"
      })

      // 1 将远程文件下载到小程序的内存中 tempFilePath
      const result1 = await uni.downloadFile({ url: this.imgDetail.img });
	  // console.log(result1);
      const { tempFilePath } = result1[1];

      //  2 将小程序内存中的临时文件下载到本地上
      const result2 = await uni.saveImageToPhotosAlbum({ filePath: tempFilePath });
      // console.log(result2);
	  
      // 3 提示用户下载成功
      uni.hideLoading();
      await uni.showToast({
        title:"下载成功"
      })
    }
  }
};
</script>

<style lang="scss"  scoped >
.user_info {
  display: flex;
  padding: 20rpx;
  .user_icon {
    padding: 0 20rpx;
    image {
      width: 88rpx;
      border-radius: 50%;
    }
  }

  .user_desc {
    .user_name {
      color: $color;
      font-weight: 600;
    }

    .user_time {
      color: #ccc;
      font-size: 24rpx;
      padding: 10rpx 0;
    }
  }
}
.user_rank {
  display: flex;
  height: 80rpx;
  border-bottom: 5rpx solid #eee;
  .rank {
    display: flex;
    justify-content: center;
    align-items: center;

    flex: 1;
    .iconfont {
    }
  }

  .user_collect {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    .iconfont {
    }
  }
}

.high_img {
  image {
    border-bottom: 1rpx solid #eee;
  }
}

// 专辑
.album_wrap {
  padding: 20rpx;
  .album_title {
    padding: 10rpx 0;
  }

  .album_list {
    .album_item {
      display: flex;
      padding: 10rpx 0;
      border-bottom: 10rpx solid #eee;
      .album_cover {
        flex: 1;
        image {
          width: 180rpx;
          height: 180rpx;
        }
      }

      .album_info {
        flex: 3;
        padding-left: 20rpx;
        position: relative;
        .album_info_text {
          width: 100rpx;
          height: 50rpx;
          background-color: $color;
          color: #fff;
          display: flex;
          justify-content: center;
          align-items: center;
        }

        .album_name {
          padding: 10rpx 0;
          color: #888;
        }
        .iconfont {
          font-size: 40rpx;
          position: absolute;
          top: 50%;
          // css3 变换 转换
          transform: translateY(-50%);
          right: 10%;
          color: #000;
        }
      }
    }
  }
}

// 最热评论
.comment {
  .comment_title {
    padding: 15rpx;
    .iconfont {
      color: red;
      font-size: 40rpx;
    }

    .comment_text {
      font-weight: 600;
      font-size: 28rpx;
      color: #666;
      margin-left: 10rpx;
    }
  }

  .comment_list {
    .comment_item {
      border-bottom: 15rpx solid #eee;
      // 用户信息
      .comment_user {
        display: flex;
        padding: 20rpx 10rpx;
        .user_icon {
          width: 15%;
		  height: 88rpx;
		  border: 1px solid #EEEEEE;
		  border-radius: 6rpx;
          display: flex;
          justify-content: center;
          align-items: center;
          image {
            width: 90%;
          }
        }

        .user_name {
          flex: 1;
          .user_nickname {
            color: #777;
          }

          .user_time {
            color: #ccc;
            font-size: 24rpx;
            padding: 5rpx;
          }
        }

        .user_badge {
          image {
            width: 40rpx;
            height: 40rpx;
            display: inline-block;
          }
        }
      }
      // 评论数据
      .comment_desc {
        display: flex;
        padding: 30rpx 10rpx;
        .comment_content {
          flex: 1;
          padding-left: 15%;
          color: #000;
        }

        .comment_like {
          text-align: right;
          .icondianzan {
          }
        }
      }
    }
  }
}

// 最新评论
.new {
  .iconpinglun {
    color: #4CD964 !important;
  }
}

// 下载
.download {
  height: 120rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  .download_btn {
    width: 90%;
    height: 80%;
    background-color: $color;
    color: #fff;
    font-size: 50rpx;
    font-weight: 600;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>