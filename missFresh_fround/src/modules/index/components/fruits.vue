<template>
	<div class="hot_container">
		<div class="load_img" v-if="isloading"><img src="/media/images/loading-gif.gif" /></div>
		<div v-if="!isloading">
			<mt-loadmore :bottomDistance="0" :top-method="loadTop" :bottom-method="loadBottom" :bottom-all-loaded="allLoaded" ref="loadmore" :bottomAllLoaded="false" @top-status-change="handleTopChange" @bottom-status-change="handleBottomChange">
				<div slot="top" class="mint-loadmore-top">
					<span v-show="topStatus !== 'loading'" :class="{ 'rotate': topStatus === 'drop' }">
						<img src="/media/images/loading-gif.gif" width="120" height="40"/>
						<p>优质生鲜 两小时到</p>
					</span>
					<span v-show="topStatus === 'loading'">
			      	<img src="/media/images/loading-gif.gif" width="120" height="40"/>
			      	<p>优质生鲜 两小时到</p>
			      </span>
				</div>
				<div class="banner">
					<mt-swipe :auto="4000">
						<mt-swipe-item v-for="(value,i) in imgs" :key="i"><img :src="'http://localhost:4000/static/upload/'+value.icon" /></mt-swipe-item>
					</mt-swipe>
				</div>
				<div class="goods">
					<div class="title">
						<span>今日特卖</span>
					</div>
					<div class="goods_list">
						<ul>
							<li v-for="(goods_list,i) in goods_lists" v-if="(goods_list.buy_permission===0)" @click="gotodetail(goods_list.sku)">
								<div class="li_container">
									<div class="img_left">
										<img :src="'http://localhost:4000/static/upload/'+goods_list.promote_tag" v-lazy="goods_list.promote_tag" />
										<img :src="'http://localhost:4000/static/upload/img_20170605114049455.png'" />
									</div>
									<div class="message_right">
										<div class="message_title">
											{{goods_list.name}}
										</div>
										<div class="message_info">
											{{goods_list.subtitle}}
										</div>
										<div class="message_des">
											<!--<span v-for="value in goods_list.product_tags">{{value.name}}</span>-->
											<span>{{goods_list.product_tagName}}</span>
										</div>
										<div class="message_highprice">
											<span>可用券价 ¥{{(goods_list.vip_price_pro.price_up.price)/100}}</span>
										</div>
										<div class="message_lowprice">
											<span>商城价 <span>¥&nbsp;</span><span>&nbsp;{{(goods_list.vip_price_pro.price_down.price)/100}}</span></span>
										</div>
										<div class="message_cart">
											<img :src="'http://localhost:4000/static/upload/img_20170425134548759.png'" @click="gotocart($event,goods_list)" />
										</div>
									</div>
								</div>
							</li>
							<div class="sell_out" v-else-if="goods_list.code=='sell-out'">
								<span>{{goods_list.name}}</span><span>{{goods_list.second_title}}</span>
							</div>
							<div class="no_banner" v-else-if="goods_list.banner==undefined">
								<span>{{goods_list.name}}</span><span>{{goods_list.second_title}}</span>
							</div>
							<div class="img_container" v-else>
								<img v-for="(value,i) in goods_list.banner" :src="value.path" :key="i" />
							</div>
						</ul>
					</div>
				</div>
				<div slot="bottom" class="mint-loadmore-bottom">
					<span v-show="bottomStatus !== 'loading'" :class="{ 'rotate': bottomStatus === 'drop' }">
			      </span>
					<span v-show="bottomStatus === 'loading'">
			      </span>
				</div>
				<div>{{look()}}</div>
			</mt-loadmore>
		</div>

	</div>
</template>

<script>
	import Vue from "vue"
	import { Swipe, SwipeItem } from "mint-ui"
	import { Loadmore } from "mint-ui"
	import { Lazyload } from 'mint-ui';
	import "mint-ui/lib/style.css"
	import axios from "axios"
	import store from "../scripts/vuex/store.js"
	Vue.component(Loadmore.name, Loadmore);
	Vue.component(Swipe.name, Swipe);
	Vue.component(SwipeItem.name, SwipeItem);
	Vue.use(Lazyload);
	export default {
		props: ["type"],
		data() {
			return {
				imgs: [],
				goods_lists: [],
				godds_lists_all: [],
				page_num: 7,
				topStatus: '',
				bottomStatus: '',
				allLoaded: false,
				store,
				num: this.$route.params,
				looked: 'fruit',
				isloading: true
			}
		},
		mounted() {
			console.log(this.store.state.type);
			axios({
				url: '/api/goods/search',
				method: 'post',
				data: {
					index: 'position',
					message: this.store.state.type,
					range: ''
				}
			}).then((res) => {
				this.goods_lists_all = res.data.products;
				this.goods_lists = this.goods_lists_all.slice(0, 7);
				this.isloading = false;
				console.log(this.goods_lists);
			})
			axios({
				url: '/api/rangler/search',
				method: 'post',
				data: {
					index: 'position',
					message: this.store.state.type
				}
			}).then((res) => {
				console.log(res.data.banner,this.store.state.type);
				this.imgs = res.data.banner;
				this.isloading = false;
			})
		},
		updated() {},
		methods: {
			loadTop() {
				axios({
					url: '/api/rangler/search',
					method: 'post',
					data: {
						index: 'position',
						message: this.store.state.type
					}
				}).then((res) => {
					this.imgs = res.data.banner;
					this.isloading = false;
				})

				axios({
					url: '/api/goods/search',
					method: 'post',
					data: {
						index: 'position',
						message: this.store.state.type,
						range: ''
					}
				}).then((res) => {
					this.goods_lists_all = res.data.products;
					console.log(this.goods_lists);
					this.$refs.loadmore.onTopLoaded();
				})
			},
			loadBottom() {
				this.page_num += 7;
				this.goods_lists = this.goods_lists_all.slice(1, this.page_num);
				this.allLoaded = true;
				this.$refs.loadmore.onBottomLoaded();
			},
			handleTopChange(status) {
				this.topStatus = status;
			},
			handleBottomChange(status) {
				this.bottomStatus = status;
			},
			look() {
				var that = this;
				setInterval(function() {
					this.looked = this.store.state.type
				}.bind(this), 300)
			},
			gotodetail(params) {
				console.log(params);
				this.$router.push({
					name: 'detail',
					query: {
						sku: params
					}
				});
			},
			gotocart(event, obj) {
				var ws = new WebSocket('ws://localhost:9000/ws');
				ws.onopen = function() {
					ws.send(111)
				}
				//				var ws = new WebSocket('ws://localhost:4000/ws'); 
				//				ws.onopen=function(){  
				//		  		ws.send('add to cart');
				//		  	}; 
				event.cancelBubble = true;
				axios({
					url: '/api/carts/search',
					method: 'post',
					data: {
						index: 'skuAndbelongs',
						message: {
							sku: obj.sku,
							belongs: store.state.Loginuser
						},
						range: ''
					}
				}).then((res) => {
					if(res.data.products.length != 0) {
						console.log(res.data.products)
						axios({
							url: '/api/carts/updatemember',
							method: 'post',
							data: {
								index: res.data.products[0].sku,
								index1: res.data.products[0].belongs,
								num: res.data.products[0].num + 1,
								price_up: res.data.products[0].vip_price_pro.price_up.price,
								price_down: res.data.products[0].vip_price_pro.price_down.price,
								sku: res.data.products[0].sku,
								name: res.data.products[0].name,
								product_tagName: res.data.products[0].product_tagName,
								belongs: store.state.Loginuser
							}
						}).then((res) => {
							ws.send('gotocart');
						})
					} else {
						axios({
							url: '/api/carts/addtocarts',
							method: 'post',
							data: {
								num: 1,
								price_up: obj.vip_price_pro.price_up.price,
								price_down: obj.vip_price_pro.price_down.price,
								sku: obj.sku,
								name: obj.name,
								product_tagName: obj.product_tagName,
								promote_tag: obj.promote_tag,
								belongs: store.state.Loginuser
							}
						}).then((res) => {
							ws.send('gotocart');
						})
					}
				})
				store.commit('addtocarts', obj);
			}
		},
		watch: {
			looked() {
				this.isloading = true;
				var that = this;
				axios({
					url: '/api/rangler/search',
					method: 'post',
					data: {
						index: 'position',
						message: this.store.state.type
					}
				}).then((res) => {
					this.imgs = res.data.banner;
					this.isloading = false;
				})

				axios({
					url: '/api/goods/search',
					method: 'post',
					data: {
						index: 'position',
						message: this.store.state.type,
						range: ''
					}
				}).then((res) => {
					this.goods_lists_all = res.data.products;
					this.$refs.loadmore.onTopLoaded();
				})
			},

		}
	}
</script>

<style lang="scss">
	@import "../styles/fruits.scss";
</style>