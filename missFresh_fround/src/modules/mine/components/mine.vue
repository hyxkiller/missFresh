<template lang="html">
    <div style="width:100%;height:100%">
        <div class="m-index" v-if="show">
            <div class="change">
                <header>
                    <!-- <button @click="login">用户登录</button> -->
                    <router-link to="/Login" v-show="!isShowButton">
                        <button>用户登录</button>                
                    </router-link>
                    <div class="user" v-show="isShowButton">
                        <img src="../images/userimg.png" alt="" class="user1">
                        <p>小鲜</p>
                        <img src="../images/uservip.png" alt="" class="user2">
                    </div>
                </header>
                <section>
                    <ul class="home-total">
                        <li>
                            <p class="remain-money">
                                <i class="ng-binding">0</i>元
                            </p>
                            <p class="nb-binding">余额</p>
                        </li>
                        <li>
                            <p class="remain-money">
                                <i class="ng-binding">0</i>张
                            </p>
                            <p class="nb-binding">红包</p>
                        </li>
                        <li>
                            <p class="remain-money">
                                <i class="ng-binding">0</i>张
                            </p>
                            <p class="nb-binding">优惠券</p>
                        </li>
                        <li>
                            <p class="remain-money">
                                <i class="ng-binding">0</i>分
                            </p>
                            <p class="nb-binding">积分</p>
                        </li>               
                    </ul>
                    <div class="invite">
                        <div class="invite-img">
                            <img src="../images/invite.png" alt="">
                        </div>
                        <img src="../images/rightarrow.png" alt="">
                    </div>
                    <!-- <div class="store">
                        <p>储值有礼</p>
                        <p>储值100元送金卡</p>
                        <ul>
                            <li>
                                <span><i>100</i>元</span>
                                <span>首次充值送1天金卡</span>
                            </li>
                            <li>
                                <span><i>200</i>元</span>
                                <span>首次充值送2天金卡</span>
                            </li>
                            <li>
                                <span><i>500</i>元</span>
                                <span>首次充值送7天金卡</span>
                            </li>
                            <li>
                                <span><i>1000</i>元</span>
                                <span>首次充值送10天金卡</span>
                            </li>
                        </ul>
                    </div> -->
                    <ul class="home-list">
                        <li>
                            <span>我的订单</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                        <li>
                            <span>我的会员卡</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                        <li>
                            <span>我的地址</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                        <li>
                            <span>下载每日优鲜APP</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                        <li>
                            <span>联系客服</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                        <li>
                            <span>意见反馈</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                        <li>
                            <span>消息</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                        <li>
                            <span>关于我们</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                        <li @click="set">
                            <span>设置</span>
                            <img src="../images/rightarrow.png" alt="">                
                        </li>
                    </ul>
                </section>
            </div>
            <footer>
                <foot></foot>
            </footer>
        </div>
        <div v-else>
            <router-view></router-view> 
        </div>
    </div>
</template>
<script>
    import foot from '../../common/footer.vue';
    import store from '../../index/scripts/vuex/store';
    export default {
        data(){
            return {
                show:true,
                isShowButton: false
            }
        },
        components:{
            foot
        },
        mounted() {
        	if(!store.state.isLogin){
        		this.isShowButton = false
        	}else{
        		this.isShowButton = true
        	}
//      	console.log(store.state.isLogin);
//          store.state.isLogin ? this.isShowButton = store.state.isLogin : (store.state.Loginuser!='passerby' ? this.isShowButton = true : false)
            // store.state.isLogin ? this.$router.push('mine') : this.$router.push('Login')
        },
        methods:{
            set(){
                this.show = false; 
                this.$router.push('/setting')
            }
        }
    }
</script>
<style lang="scss">
    @import '../styles/mine.scss';
</style>