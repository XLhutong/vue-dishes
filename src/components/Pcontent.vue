<template>
    <div id="pcontent">
        <router-link to="/home">
            <div class="back">返回</div>
        </router-link>
		<div class="p_content">
			<div class="p_info">
				<img :src="api+list.img_url" />
				<h2>{{list.title}}</h2>
				<p class="price">{{list.price}}/份</p>
			</div>
			
			<div class="p_detial">
				<h3>商品详情</h3>
				<div class="p_content" v-html="list.content">
				</div>
				
			</div>
		</div>
		
		<footer class="pfooter">
			
			<div class="cart">				
				<strong>数量:</strong>
				<div class="cart_num">
		          <div class="input_left" @click="reduceNum()">-</div>
		          <div class="input_center">
		              <input type="text"  readonly="readonly" v-model="num" name="num" id="num" />
		          </div>
		          <div class="input_right" @click="addNum()">+</div>				      
		        </div>								
			</div>   
            <button class="addcart" @click="addCart()">加入购物车</button>
		</footer>
    </div>
</template>

<script>
import storage from '../model/storage.js'
import Config from '../model/config.js'
export default {
    data(){
        return{
           api:Config.api,
           list:[],
           num:1,
        }
    },
    methods:{
        recData(id){
          var api = this.api+'api/productcontent?id='+id;
          this.$http.get(api).then((response)=>{
                // console.log(response);
                this.list = response.body.result[0];
          },(err)=>{
              alert('请求失败了');
          })
        },
        reduceNum(){
            if(this.num>1){
                --this.num;
            }
        },
        addNum(){
            ++this.num;
        },
        addCart(){

            //获取数据加入购物车

            //桌号先写死  f001
            var uid = storage.get('roomid');
            var api = this.api+'api/addcart';
            this.$http.post(api,{
                uid:uid,
                title:this.list.title,
                price:this.list.price,
                product_id:this.list._id,
                num:this.num,
                img_url:this.list.img_url

            }).then((response)=>{
                console.log(response.body.success)
                if(response.body.success){
                    //加入购物车的时候给服务器发送消息
                    this.$socket.emit('addcart','addcart');
                    this.$router.push({path:'/home'})
                }
            },(err)=>{  
                alert('请求失败');
            })
        }

    },
    mounted(){
        var id = this.$route.query.id;
        this.recData(id);
    }

    
}
</script>

<style lang="scss">
.back{
    height: 3.8rem;
    line-height: 3.8rem;
    width: 3.8rem;
    border-radius: 50%;
    background: #000;
    position: fixed;
    top: .5rem;
    left: .5rem;
    color: #fff;
    
    &:before{
        content: '';
        display: block;
        height: .8rem;
        width: .8rem;
        border-left: .2rem solid #fff;
        border-bottom: .2rem solid #fff;
        float: left;
        position: relative;
        top: 1.4rem;
        left: .6rem;
        transform: rotate(45deg);
        margin-right:.4rem ;
    }
}
.p_content{
    .p_info{
        background: #fff;
        img{
            width: 100%;
            height: 18rem;
        }
        h2{
            padding:.2rem .5rem;
        }
        .price{
            padding: .5rem;
            color: red;
        }
    }
    .p_detial{
        background: #fff;
        margin-top: 1rem;
        h3{
            padding: .5rem;
        }
        .p_content{
            padding: 1rem;
            img{
                max-width: 100%;
                display: block;
                margin: 0 auto;
            }
            *{
                 line-height: 1.5rem;
                 color: #666;
            }
        }
    }
    
}
//底部
.pfooter{
    position: fixed;
    bottom: 0;
    height: 4.4rem;
    line-height: 4.4rem;
    background: #fff;
    left: 0;
    width: 100%;
    border-top: 1px solid #eee;
    
    .cart{
       float: left;
       display: flex;
       strong{
           flex: 1;
           font-size: 1.6rem;
           padding: 0 .5rem;
       }
       .cart_num{
           width: 10rem;
           display: flex;
           margin-top: .8rem;
           .input_left,.input_right{
               flex: 1;
               width: 2.8rem;
               height: 2.8rem;
               line-height: 2.8rem;
               text-align: center;
               color: red;
               border: 1px solid #eee;
               font-size: 2.4rem;
           }
           .input_center{
               flex: 1;
               input{
                   width: 100%;
                   height: 2.8rem;
                   border: none;
                   text-align: center;
                   border-top: 1px solid #eee;
                   border-bottom: 1px solid #eee;
                   float: left;
               }
           }
           
           
       }
    }
    .addcart{
        float: right;
        background: red;
        color: #fff;
        height: 3rem;
        border: none;
        padding: 0 .5rem;
        border-radius: .5rem;
        margin-top: .8rem;
        margin-right: .5rem;
    }
}

</style>
