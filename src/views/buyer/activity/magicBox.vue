<template>
	<div class="w-magicbox">
		<div class="w-mb-img">
			<mt-swipe :auto="3000">
        <mt-swipe-item>
          <img>
        </mt-swipe-item>
      </mt-swipe>
		</div>
		<div class="w-mb-cost">
			<div class="w-mb-deposit">
				<span class="w-mb-deposit-lowest">押金<span class="w-mb-deposit-lowest-small">（最低价）</span>¥100</span>
				<span class="w-mb-deposit-now">当前 ¥100</span>
				<!-- <span class="w-mb-gift"></span> -->
			</div>
			<div class="w-mb-price-bought">
				可购价 ¥ 150
			</div>
			<div class="w-mb-price-box">
				<span class="w-mb-price">价格 <span class="w-mb-price-line">¥290</span></span>
				<span class="w-mb-market">市场价 <span class="w-mb-price-line">¥290</span></span>
			</div>

		</div>
		<div class="w-mb-product-info">
			<div class="w-mb-product-title">
				<span class="w-mb-product-block">大行星</span>
				TAWA防水帐篷户外野营必备TAWA防水帐篷户外野营必备TAWA防水帐篷户外野营必备TAWA篷TAWA防水帐篷户外野营必备TAWA防水帐篷户外野营必备TAWA防水帐篷户外野营必备TAWA篷
			</div>
			<div class="w-mb-product-prop">
				<span>运费：6元</span>
				<span>运费：6元</span>
				<span>运费：6元</span>
			</div>
		</div>
		<div class="w-mb-format" @click="changeModal('show_sku',true)">
			<span class="w-mb-format-text">规格</span>
			<div class="w-mb-format-select">选择 <span>  颜色分类  重量</span></div>
			<div class="w-mb-format-more"></div>
		</div>
		<div class="w-mb-rule">
			<span class="w-mb-rule-title">规则</span>
			<span class="w-mb-rule-text">通过分享点击进入，点击自由选择ABC档…</span>
		</div>
		<div class="w-mb-record">
			<span class="w-mb-record-title">拆盒记录</span>
			<!-- <span class="w-mb-record-text">12345</span> -->
			<ul class="w-mb-record-ul">
				<li class="w-mb-record-li">
					<span class="w-mb-record-avatar"></span>
					<span class="w-mb-record-text">居居拆盒并分享，减少5元，当前155元</span>
				</li>
				<li class="w-mb-record-li">
					<span class="w-mb-record-avatar"></span>
					<span class="w-mb-record-text">居居拆盒并分享，减少5元，当前155元</span>
				</li>
			</ul>
		</div>
		<div class="w-mb-product-img">
			<img>
		</div>
		<div class="w-mb-bottom">
			<div class="w-mb-bottom-info">
				继续分享获取更多优惠
			</div>
			<div class="w-mb-bottom-btn">
				<span>分享帮拆</span>
				<span>支付差价</span>
			</div>
			
			
		</div>
	<sku v-if="show_sku" :now_select="select_value" :now_num="canums" :product="product_info" @changeModal="changeModal" @sureClick="sureClick"></sku>
	</div>
</template>

<script>
import sku from '../components/sku';
import { Swipe, SwipeItem } from 'mint-ui';
import axios from 'axios';
export default {
	data(){
		return{
			show_sku:false,
		}

	},
	components:{sku,Swipe,SwipeItem},
	methods:{
		changeModal(v,bool) {
      this[v] = bool;
      if(bool){
      	scroll.afterOpen();
  		}else{
        scroll.beforeClose();
      }
		},
		

	},
	created(){

	}
	
}
</script>

<style lang="less" rel="stylesheet/less" scoped>
	@import "../../../common/css/index";
	.w-magicbox{
		.w-mb-img{
			width: 750px;
			height: 750px;
			background-color: black;
		}
		.w-mb-cost{
			// width:100%;
			padding: 20px 24px;
			// .flex-row(flex-start);
			// flex:1;
			
			.w-mb-deposit{
				width: 100%;
				.flex-row(space-between,flex-end);
				font-size: 40px;
				font-weight: 600;
				
				.w-mb-deposit-lowest{
					color: #E22300;
					.w-mb-deposit-lowest-small{
						font-size: 24px;
					}
				}
				.w-mb-deposit-now{
					color: @mainColor;
				}
				.w-mb-gift{
					display: inline-block;
					width: 40px;
					height: 40px;
					margin-left: 20px;
					background: url("./static/images/activity/mb-gift.png") no-repeat;
					background-size: 100% 100%;
				}
			}
			.w-mb-price-bought{
				text-align: left;
				padding: 10px 0;
				color: #000;
			}
			.w-mb-price-box{
				width:100%;
				.flex-row(space-between);
				.w-mb-price-line{
					text-decoration: line-through;
				}
				.w-mb-price{
					font-size:24px;
					color: #C1C1C1;
				}
				.w-mb-market{
					font-size:24px;
					color: #000;
				}
			}
		}
		.w-mb-product-info{
			border-bottom: 1px solid #F2F2F2;
			.w-mb-product-title{
				padding: 0 24px;
				text-align: left;
				display: -webkit-box;
				-webkit-line-clamp: 2;
				-webkit-box-orient: vertical;
				overflow: hidden;
				text-overflow: ellipsis;
				.w-mb-product-block{
					display: inline-block;
					text-align: center;
					width:92px;
					height:33px;
					line-height: 33px;
					color: #fff;
					background: linear-gradient(270deg,@subColor 0%,@mainColor 100%);
				}
			}
			.w-mb-product-prop{
				padding: 20px 24px;
				color: #C1C1C1;
				.flex-row(space-between);
			}
		}
		.w-mb-format{
			padding: 25px 24px;
			border-bottom: 1px solid #F2F2F2;
			.flex-row(space-between);
			.w-mb-format-text{
				display: inline-block;
				margin-right: 90px;
				color: #666666;
			}
			.w-mb-format-select{
				width:400px;
				flex: 1;
				text-align:left;
			}
			.w-mb-format-more{
				// display: flex;
				width:17px;
				height:30px;
				background: url("./static/images/activity/mb-more.png") no-repeat;
				background-size: 100% 100%;
				// justify-self: flex-end;
			}
		}
		.w-mb-rule{
			padding: 25px 24px;
			border-bottom: 1px solid #F2F2F2;
			.flex-row(flex-start);
			.w-mb-rule-title{
				display: inline-block;
				margin-right: 90px;
				color: #666666;
			}
			.w-mb-rule-text{
				flex: 1;
				text-align: left;
			}
		}
		.w-mb-record{
			padding: 15px 24px;
			.flex-row(flex-start,flex-start);
			.w-mb-record-title{
				display: inline-block;
				margin: 15px 25px 0 0;
				color: #666666;
			}
			.w-mb-record-ul{
				.w-mb-record-li{
					padding-bottom: 22px;
					.flex-row(flex-start);
					.w-mb-record-avatar{
						display: inline-block;
						width: 60px;
						height: 60px;
						border-radius: 30px;
						margin-right: 10px;
						border: 2px solid @mainColor;
						background-color: @subColor;

					}
					.w-mb-record-text{
						display: inline-block;
						height: 60px;
						line-height: 60px;

					}
				}

			}
		}
		.w-mb-bottom{
			width: 100%;
			position: absolute;
			bottom: 0;
			.w-mb-bottom-info{
				height: 98px;
				line-height: 98px;
				color:@mainColor;
				font-size:30px;
				font-weight:500;
			}
			.w-mb-bottom-btn{
				display: flex;
				span{
				display: inline-block;
				height: 98px;
				line-height: 98px;
				font-size:30px;
				font-weight:500;
				color: #fff;
				flex:1;
				background:linear-gradient(313deg,@mainColor 0%,@subColor 100%);
			}
			}
			
		}
	}
</style>
