<template>
    <div class="bgf8">
        <div class="header bgf8">
            <p class="fl">总资产折合：<span class="asset_num">0.0000000</span><span class="asset_name"> BTC</span><span class="ft12 "> ≈ <span>0.00</span>CNY</span>
            <label class="min_lab ft14"><input type="checkbox" />隐藏小额资产</label><i></i><label class="inp_lab"><input  type="text"/><i></i></label>
            </p>
            <p class="fr right_text">
                <!-- <span class="record" @click="record">财务记录</span> -->
                <span class="address" @click="withdraw_address">提币地址管理</span>
            </p>
        </div>
        <div class="content  ft12">
           <div class="content_top flex alcenter fColor2">
               <p class="flex1 tc">币种<i></i></p>
               <p class="flex1 tc">可用</p>
               <p class="flex1 tc">冻结</p>
               <!-- <p class="flex1 tc">BTC估值<i></i></p> -->
               <!-- <p class="flex1 tc">锁仓</p> -->
               <p class="flex1 tc">操作</p>
           </div>
           <ul class="content_ul">
               <li v-for="(item,index) in asset_list" :key="index">
                    <div class="content_li flex alcenter between">
                   <p class="flex1 tc">{{item.currency_name}}</p>
                   <p class="flex1 tc">{{item.change_balance}}</p>
                   <p class="flex1 tc">{{item.lock_change_balance}}</p>
                   <!-- <p class="flex1 tc">{{item.valuation}}</p> -->
                   <!-- <p class="flex1 tc">{{item.lock_position}}</p> -->
                   <p class="flex1 tc operation">
                       <span @click="excharge(index,item.currency)" >充币</span>
                       <span @click="withdraw(index,item.currency)">提币</span>
                       <!-- <span @click="exchange">兑换</span> -->
                       <span @click="rec(index)">记录</span>
                   </p>
                   </div>
                   <!--充币区-->
                   <div class="hide_div" v-if="index == active">
                       <p class="fColor2 ft12">充币地址</p>
                       <p class="mt50 mb50"><span class="ft18  excharge_address" :class="{'bg':flags}">{{excharge_address}}</span><span id="copy" @click="copy" class="copy ft14">复制</span><span class="ewm_wrap"><span class="ewm ft14" @click="show_ewm">二维码</span>
                         <div class="ewm_img" id="code" :class="{'hide':isHide}">
                             
                         </div>
                         <!-- <img class="ewm_img" :class="{'hide':isHide}" src="../../assets/images/ewm.jpg" /> -->
                       </span></p>
                       <p class="ft12 fColor2 mb50">查看<span class="excharge_record">充币记录</span>跟踪状态</p>
                       <p class="ft12 fColor2 mb15">温馨提示</p>
                       <ul class="tips_ul ft12 fColor2" style="list-style:disc inside">
                           <li class="tips_li" style="list-style:disc inside" v-for="item in tip_list">{{item}}</li>
                       </ul>
                   </div>
                   <!--提币区-->
                   <div class="hide_div" v-if="index == active01">
                       <p class="fColor2 ft12 mb15">提币地址</p>
                       <input class="address_inp  mb30" type="text" v-model="address" />
                       <p class="fColor2 ft12 mb15 flex between alcenter"><span>数量</span><span>可用：<span class="use_num">{{balance}}</span><span>限额：<span>1500.000000000</span><span class="advance">提升额度</span></span></span></p>
                       <label class="num_lab flex between mb30">
                            <input class="" type="text" :placeholder="min_number" v-model="number" />
                            <span>{{coinname}}</span>
                        </label>
                       <div class="flex mb50">
                           <div class="left_inp_wrap flex1">
                               <p class="fColor2 ft12 mb15">
                                   <span>手续费</span>
                                   <span>范围：<span>{{ratenum}}</span></span>
                               </p>
                               <label class="range_lab flex alcenter between"><input class=""  type="text" v-model="rate" /><span>{{coinname}}</span></label>
                           </div>
                           <div class="right_inp_wrap flex1">
                               <p class=" mb15">
                                   <span class="fColor2 ft12">到账数量</span>
                               </p>
                               <label class="get_lab flex alcenter between"><input class="" disabled v-model="reachnum" type="number" /><span>{{coinname}}</span></label>
                           </div>
                       </div>
                       <div class="flex">
                        <div class="flex2">
                       <p class="ft12 fColor2 mb15">温馨提示</p>
                       <ul class="tips_ul ft12 fColor2" style="list-style:disc inside">
                           <li class="tips_li" style="list-style:disc inside" v-for="item in tip_list01">{{item}}</li>
                       </ul>
                       </div>
                       <div class="flex1 tc"><button class="withdraw_btn" @click="mention">提币</button></div>
                       
                       </div>
                   </div>
                   <!--记录区-->
                   <div class="hide_div rec-box" v-if="index == active02">
                       <div class="rec-con">

                        <div class="rec-title">
                            <span>数量</span>
                            <span>记录</span>
                            <span>时间</span>
                        </div>
                        <ul class="rec-list">
                            <li v-for="(reItem,reIndex) in recData[index]" :key="reIndex">
                                <span>{{reItem.value}}</span>
                                <span>{{reItem.info}}</span>
                                <span>{{reItem.created_time}}</span>
                            </li>
                            
                        </ul>
                       </div>
                   </div>
               </li>
           </ul>
           <!-- <div class="tc ft16  mt50" v-show="asset_list.length<=0">暂无数据</div> -->
        </div>
    </div>
</template>
<script>
import indexHeader from '@/view/indexHeader'
import left from '@/view/left'
import "@/lib/clipboard.min.js"
import "@/lib/jquery.qrcode.min.js"
export default {
    name:'finance',
    data(){
        return{
            recData:[],
            token:'',
            flags:false,
            flag:false,
            isHide:true,
            active:'a',
            active01:'a',
            active02:'a',
            addr:'',
            url:'',
            excharge_address:'',
            address:'',
            number:'',
            rate:'',
            coinname:'',
            balance:'',
            ratenum:'',
            reachnum:'',
            min_number:'',
            currency:'',
            asset_list:[],
            tip_list:[
                '请勿向上述地址充值任何非USDT资产，否则资产将不可找回。','USDT充币仅支持simple send的方法，使用其他方法（send all）的充币暂时无法上账，请您谅解。','请勿向上述地址充值任何非USDT资产，否则资产将不可找回。','USDT充币仅支持simple send的方法，使用其他方法（send all）的充币暂时无法上账，请您谅解。'
            ],
            tip_list01:[
                '请勿向上述地址充值任何非USDT资产，否则资产将不可找回。','USDT充币仅支持simple send的方法，使用其他方法（send all）的充币暂时无法上账，请您谅解。','请勿向上述地址充值任何非USDT资产，否则资产将不可找回。','USDT充币仅支持simple send的方法，使用其他方法（send all）的充币暂时无法上账，请您谅解。'
            ]
        }
    },
    components:{
        indexHeader,
        left
    },
    methods:{
        goRecord(){
            this.$router.push({name:'coinRecord'})
        },
        init(){
             var clipboard = new Clipboard('.copy')
            clipboard.on('success', function (e) {
               layer.alert('复制成功')
            });
            clipboard.on('error', function (e) {
                alert('复制失败')
            });
        },
        //充币
        excharge(index,currency){
            console.log(currency);
            this. currency= currency;
            if(this.flag){
                this.flag = false;
                this.active = 'a';
                this.active01 = 'a';
                 this.active02 = 'a';
            }else{
                this.flag = true;
                this.active = index;
                this.active01 = 'a';
                 this.active02 = 'a';
            }
            this.sendData(currency)
        },
        sendData(currency){
            var that = this;
            // $.ajax({
            //     type: "POST",
            //     url: this.$utils.laravel_api + 'wallet/get_in_address',
            //     data: {
            //         currency:currency
            //     },
            //     dataType: "json",
            //     async: true,
            //     beforeSend: function beforeSend(request) {
            //         request.setRequestHeader("Authorization", that.token);
            //     },
            //     success: function(res){
            //         if (res.type=="ok"){
            //             console.log(res)
            //             that.excharge_address=res.message;
            //             // 生成二维码
            //             $('#code').qrcode({
            //                 width: 100, //宽度
            //                 height:100, //高度
            //                 text:res.message
            //             });
            //         }else{
            //             console.log(res.message)
            //         }
            //     }
            // })
            this.$http({
                url: '/api/' + 'wallet/get_in_address',
                method:'post',
                data:{currency:currency},
                headers: {'Authorization':  that.token},
                }).then(res=>{
                    console.log(res)
                    if (res.data.type=="ok"){
                        that.excharge_address=res.data.message;
                        // 生成二维码
                        $('#code').qrcode({
                            width: 100, //宽度
                            height:100, //高度
                            text:res.data.message
                        });
                    }else{
                        console.log(res.data.message)
                    }
                }).catch(error=>{
                    console.log(error)
            })
        },
        //提币
        withdraw(index,currency){
            this.currency=currency;
             if(this.flag){
                this.flag = false;
                this.active = 'a';
                this.active01 = 'a'
                 this.active02 = 'a';
            }else{
                this.flag = true;
                 this.active01 = index;
                 this.active = 'a';
                 this.active02 = 'a';
            }
            this.getNum(currency);
        },
        //记录
        rec(index,currency){
             if(this.flag){
                this.flag = false;
                this.active = 'a';
                this.active01 = 'a'
                 this.active02 = 'a';
            }else{
                this.flag = true;
                 this.active02 = index;
                 this.active = 'a';
                  this.active01 = 'a'
                
            }
        },
        getNum(currency){
            var that = this;
            $.ajax({
                type: "POST",
                url: this.$utils.laravel_api + 'wallet/get_info',
                data: {
                    currency:currency
                },
                dataType: "json",
                async: true,
                beforeSend: function beforeSend(request) {
                    request.setRequestHeader("Authorization", that.token);
                },
                success: function(res){
                    if (res.type=="ok"){
                        console.log(res)
                        that.coinname=res.message.name;
                        that.balance=res.message.change_balance;
                        that.min_number='最小提币数量'+res.message.min_number;
                        that.minnumber=res.message.min_number;
                        that.ratenum=res.message.rate+'-'+res.message.rate;
                        that.reachnum=0.0000;
                        that.rate=res.message.rate;
                        
                    }else{
                        console.log(res.message)
                    }
                }
            })
        },
        // 提币按钮
        mention() {
            var that =this;
            var currency = this. currency;
            var address = this.address;
            var number = this.number;
            var rate = this.rate;
            var min_number = this.minnumber;
            if(!address){
                layer.alert('请输入提币地址');
                return;
            } 
            if(!number){
                layer.alert('请输入提币数量');
                return;
            } 
            if((number-0)<min_number){
                console.log(number,min_number)
                return layer.alert('输入的提币数量小于最小值');
            }
            // if(rate=='' || rate>=1){
            //     layer.alert('请输入0-1之间的提币手续费');
            //     return;
            // }
            $.ajax({
                type: "POST",
                url: this.$utils.laravel_api + 'wallet/out',
                data: {
                    currency:currency,
                    number:number,
                    rate:rate,
                    address:address
                },
                dataType: "json",
                async: true,
                beforeSend: function beforeSend(request) {
                    request.setRequestHeader("Authorization", that.token);
                },
                success: function(res){
                    console.log(res)
                    if (res.type=="ok"){
                        layer.alert(res.message)
                        setTimeout(() => {
                          window.location.reload();
                    }, 1500);
                    }else{
                        layer.alert(res.message)
                    }
                }
            })
            
        },
        exchange(){

        },
        //复制
        copy(){
            var that=this;
          var clipboard = new Clipboard('.copy',{
                    text:function(){
                        return that.excharge_address
                    }
                });
          clipboard.on("success", function (e) {
                        that.flags = true;
                        layer.msg('复制成功');
                        
                    });
                    clipboard.on("error", function (e) {
                        that.flags = false;
                         layer.msg('请重新复制')
                    });
        },
        record(){
            this.$router.push({ name: 'finanrec' });
        },
        withdraw_address(){
            this.$router.push({ name: 'withdraw_address' });
        },
        show_ewm(){
            if(this.isHide){
                this.isHide = false
            }else{
                this.isHide = true
            }
        },
        getdata(){
            var that = this;
            console.log(that.token)
            // $.ajax({
            //     url: this.$utils.laravel_api + "wallet/list",
            //     type: "POST",
            //     dataType: "json",
            //     async: true,
            //     beforeSend: function beforeSend(request) {
            //        request.setRequestHeader("Authorization", that.token);
            //     },
            //     success: function (data) {
            //     console.log(data)
            //     if (data.type == 'ok') {
            //         that.asset_list=data.message.change_wallet.balance;
            //     } else if (data.type == '999') {
                    
            //     }
            //     }
            // });
            this.$http({
            url: '/api/' + 'wallet/list',
            method:'post',
            data:{},
            headers: {'Authorization':  that.token},
            }).then(res=>{
                console.log(res.data)
                that.asset_list=res.data.message.change_wallet.balance;
                this.asset_list.forEach((item,index) => {
                    this.$http({
                        url: '/api/wallet/legal_log',
                        method:'post',
                        data:{type:'change',currency:item.currency},
                        headers:{'Authorization':this.token}
                    }).then( res => {
                        console.log(res);
                        if(res.data.type == 'ok'){
                            this.recData[index] = res.data.message.list;
                        }
                    })
                })
            }).catch(error=>{
                console.log(error)
            })
        }
    },
    created(){
        this.token= localStorage.getItem('token') || '';
        // this.address=localStorage.getItem('address') || '';
        // console.log(this.address)
        // if(this.address){
        //     this.$http({
        //         url:this.$utils.laravel_api+'money/rechange?user_id='+this.address,
        //         type:'GET'
        //     }).then(res=>{
        //         console.log(res)
        //         this.addr=res.data.message.company_eth_address;
        //         this.url='http://qr.liantu.com/api.php?&w=300&text='+res.data.message.company_eth_address;
        //         var content = this.addr;
        //         // var clipboard = new Clipboard('#copy')
        //     }).catch(error=>{
        //         return error
        //     })
        // }
    },

    mounted(){
        var that = this;
        that.getdata();
        this.init();
    }
};
</script>
<style scoped lang="scss"> 
    
    .header{
        padding: 15px 30px;
        overflow: hidden;
    }
    .min_lab{
        margin: 0 10px;
    }
    .min_lab input{
        margin-right: 3px;
    }
    .inp_lab{
        border: 1px solid #6b80ae;
        border-radius: 2px;
        padding: 3px 5px;
    }
    .inp_lab input{
        background: none;
        border: none;
        width: 120px;
        
    }
    .right_text{
        color: #d45858;
    }
    .right_text span{
        cursor: pointer;
    }
    .record{
        margin-right: 10px;
    }
    .content_ul{
        padding: 0 20px;
    }
    .content_top{
        padding: 10px 20px;
        // background: #161617c7;
    }
    .content_li{
        padding: 15px 0;
        border-bottom: 1px solid #303b4b;
    }
    .operation,.copy,.ewm{
        color: #d45858;
    }
    .copy{
        margin: 0 30px;
    }
    .copy:hover{
        cursor: pointer;
    }
    .ewm:hover{
        cursor: pointer;
    }
    .operation span{
        cursor: pointer;
    }
    .hide_div{
        border:1px solid #303b4b;
        padding: 20px;
    }
    .excharge_record{
        color: #d45858;
    }
    input{
        background: none;
        border: none;
    }
    .address_inp{
        width: 100%;
        border: 1px solid #6b80ae;
        border-radius: 3px;
        padding: 15px 15px;
    }
    .num_lab{
        display: flex;
        width: 100%;
        border: 1px solid #6b80ae;
        border-radius: 3px;
        padding: 15px;
    }
    .num_lab input{
        width: 100%;
    }
    .range_lab,.get_lab{
         border: 1px solid #6b80ae;
        border-radius: 3px;
        padding: 15px;
    }
    
    .right_inp_wrap{
        margin-left: 20px;
    }
    .use_num,.advance{
        color: #d45858;
    }
    .use_num{
        margin-right: 5px;
    }
    .advance{
        margin-left: 5px;
    }
    .withdraw_btn{
        background-color: #d45858;
        color: #fff;
        padding: 15px 70px;
        border: none;
        border-radius: 5px;
    }
    .withdraw_btn:hover{
        cursor: pointer;
    }
    .bg{
        // background: #2b3c71;
    }
    .ewm_wrap{
        position: relative;
    }
    .ewm_img{
        width: 120px;
        height: 120px;
        position: absolute;
        top: 25px;
        left: -30px;
        // border: 10px solid #262a42;
    }
    .hide{
        display: none;
    }
    .rec-box{
        
        .rec-con{
            margin: 10px;
            padding: 0 20px;
            // background: #262a42;
                span{
                    flex:1;text-align: center;
                    line-height: 3;
                }
            .rec-title{
                display: flex;
                justify-content: space-between;
                font-size: 14px;
                // color:#fff;
                line-height: 1.5;
            }
            li{
                display: flex;
                
                justify-content: space-between;
                font-size: 12px;
                // color: #728daf;
                // border-top: 1px solid #181b2a;
            }
        }
    }
</style>


