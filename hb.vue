<template>
    <div id="hb">
        <div class="hbTop">
            <div class="hbTopLeft" style="color: #FFFFFF">关闭</div>
            <div class="hbTopCenter">电子签名</div>
            <div class="hbTopright" @click="dzqm">关闭</div>
        </div>
        <div class="hbcenter">
            <div class="hbcenterBox">
                <vueSignature ref="signature" :sigOption="option" :w="'100%'" :h="'100%'"></vueSignature>
                <div class="hbimg" v-show="isImg">
                    <img :src="img" alt="">
                </div>
            </div>
        </div>
        <div class="hbBottom">
            <div class="hbtitle">
                请在手写板上签署您的电子签名,签署后点击"生成签名"
            </div>
            <div class="hbBtn">
                <button @click="clear">重写</button>
                <button @click="save">生成签名</button>
            </div>
        </div>
        <vueSignature ref="signature1" :sigOption="option"></vueSignature>
    </div>
</template>

<script>
    import store from '../../../store/index'
    import {upSignImg} from '../../../api/api'
    export default {
        name: "app",
        data() {
            return {
                option:{
                    penColor:"#333333",
                },
                img:'',
                isImg:false,
                uid:''|| store.state.common.userid,
            };
        },
        methods:{
            dzqm(){
                if(this.$route.query.proof_id){
                    this.$router.push({path: '/louDetails', query:{uid:this.uid,proof_id:this.$route.query.proof_id,lender_sign:this.$route.query.sign_img,type:this.$route.query.type}})
                }else{
                    this.$router.push({ path: '/lous',query:{id:6}})
                }
            },
            save(){
                if(this.$route.query.sign_img !=''){
                    if(this.isImg==true){
                        this.$confirm({
                            title:'提示',
                            content: '您没有重新签名,我们将默认原有签名',
                            yesStyle: {color:'#ccc'}, // 设置左边按钮样式
                            yesText: '取消',  // 左边按钮文本,
                            noStyle: {},  // 设置右边按钮样式,
                            noText: '确定',   // 设置右边按钮文本
                            // component: Component // 可不设置, 适用于用户自定义组件.
                        }).then(
                            ()=>{

                            }
                        ).catch(
                            ()=> {
                                if(this.$route.query.proof_id){
                                    this.$router.push({path: '/louDetails', query:{uid:this.uid,proof_id:this.$route.query.proof_id,lender_sign:this.$route.query.sign_img,type:this.$route.query.type}})
                                }else{
                                    this.$router.push({ path: '/lous',query:{id:6}})
                                }
                            }
                        )
                        return;
                    }
                }
                    var _this = this;
                    var sign_img = _this.$refs.signature.save()
                    upSignImg(store.state.common.userid,sign_img).then((res)=>{
                        if(res.code==1000){
                            if(_this.$route.query.proof_id){
                                  _this.$router.push({path: '/louDetails', query: {uid:_this.uid,proof_id:_this.$route.query.proof_id,lender_sign:res.data.sign_img,type:this.$route.query.type}})
                            }else{
                                  _this.$router.push({ path: '/lous',query:{id:6}})
                            }
                        }else{
                            _this.$toast(res.msg);
                        }
                    })
            },
            clear(){
                this.isImg=false;
                var _this = this;
                _this.$refs.signature.clear();
            }
        },
        mounted(){
            if(this.$route.query.sign_img !=''){
                this.img=this.$route.query.sign_img;
                this.isImg=true;
            }
        }
    };
</script>
<style scoped>
    #hb{
        width: 100%;
        height: 100%;
        background: #FFFFFF;
    }
    #hb .hbTop{
        width: 100%;
        height: 0.88rem;
        padding: 0 0.4rem;
        border-bottom:0.02rem solid #cccccc;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
    #hb .hbTop .hbTopCenter{
        font-size: 0.32rem;
        color: #333333;
        letter-spacing: 0.02rem;
    }
    #hb .hbTop .hbTopright{
        font-size: 0.26rem;
        color: #999999;
    }
    #hb .hbcenter{
        width: 7.5rem;
        height: 4.4rem;
        padding: 0.4rem;

    }
    #hb .hbcenter .hbcenterBox{
        width: 100%;
        height: 100%;
        background: #f5f5f9;
        position:relative;
    }
    .hbimg{
        position: absolute;
        left:0;
        top:0;
        width:100%;
        height:100%;
        overflow: hidden;
    }
    #hb .hbBottom{
        width: 100%;
        height: calc(100% - 10.38rem);
        padding: 0 0.4rem;
    }
    #hb .hbBottom .hbtitle{
        font-size: 0.2rem;
        color: #1d93ec;
        letter-spacing: 0.02rem;
        line-height: 0.4rem;
    }
    #hb .hbBottom .hbBtn{
        width: 100%;
        height: 0.8rem;
        margin:0.4rem auto;
        display: flex;
        justify-content: space-around;
    }
    #hb .hbBottom .hbBtn button{
        width: 30%;
        height: 100%;
        background: #1d93ec;
        color: #ffffff;
        border-radius: 0.1rem;
        font-size: 0.28rem;
        border:none;
        outline: none;
    }
    #hb .hbBottom .hbBtn button:first-child{
        background: none;
        border:0.02rem solid #1d93ec;
        color: #1d93ec;
    }
</style>