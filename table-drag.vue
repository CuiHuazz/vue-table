<template>
    <div id="index" v-on:mouseup="mouseup" :class="ifGo?'no-select-text':null" v-on:mousemove="mousemove($event)">
        <table id="headTable" cellpadding="0" cellspacing="0" ref="moveTb" :style="'width:'+tabWidth+'px'">
            <tr>
                <td v-for="(item,index) in titleData" :key="index" v-on:mouseup.stop="mouseup"
                    v-on:mousedown.stop="mousedown(index)" v-on:mouseenter.stop="mouseenter(index)" :style="'width:'+item.width+'px'"
                    :class="endIndex==0?
                    (index == endIndex?'left-red table-title super':'table-title super'):starIndex<endIndex?
                    (index == endIndex?'right-red table-title super':'table-title super'):(index == endIndex-1?
                    'right-red table-title super':'table-title super')">
                    {{item.title}}
                    <div class="cut" v-on:mouseup.stop="cutSeup($event)" v-on:mouseenter.stop="cutSeenter(index)" v-on:mousedown.stop="cutDown(index,$event)"></div>
                </td>
            </tr>
            <tr v-for="(item,index) in mainData" :key="index">
                <td v-for="(td,tdx) in titleData" :key="tdx">{{item[td.name]}}</td>
            </tr>
        </table>
        <div id="info" v-if="ifGo" ref="info" :style="'left:'+sX+'px;top:'+sY+'px'">
            {{tdItem}}
        </div>
    </div>
</template>


<script>
    export default {
        props:['titleData','mainData'],
        computed:{
            tabWidth:function(){
                let width = 0;
                this.titleData.forEach((item) => {
                    width += item.width;
                })
                return width;
            }
        },
        methods: {
            //td鼠标按下事件
            mousedown(index){
                this.ifGo = true;
                this.starIndex = index;
                this.tdItem = this.titleData[this.starIndex].title;
            },
            //td鼠标经过事件
            mouseenter(index){
                if(this.ifGo){
                    this.endIndex = index;
                }
            },
            //td鼠标放开事件
            mouseup(){
                if(this.starIndex == -1 || this.endIndex ==-1){
                    this.ifGo = false;
                    this.cutGo = false;
                    return false;
                }
                this.ifGo = false;
                this.cutGo = false;
                let a = this.titleData[this.starIndex];
                this.titleData.splice(this.starIndex,1);
                this.titleData.splice(this.endIndex, 0, a);
                this.starIndex = -1;
                this.endIndex = -1;
            },
            //td鼠标移动事件
            mousemove(ev){
                let e = ev || event;
                if (this.ifGo&&!this.cutGo) {
                    this.sX = e.clientX + 5;
                    this.sY = e.clientY -50;
                    if (e.preventDefault) {
                        e.preventDefault();
                    }
                    return false;
                }else if(!this.ifGo && this.cutGo && this.cutIndex != -1){
                    this.titleData[this.cutIndex].width = this.tdOldWidth + (e.x - this.oldX); 
                }
            },
            //边界鼠标放开事件
            cutSeup(ev){
                let e = ev || event;
                this.oldX = e.x;
                this.oldWidth = this.$refs.moveTb.rows[0].clientWidth;
                this.cutGo = false;
            },
            //边界鼠标按下事件
            cutDown(index,ev){
                let e = ev || event;
                this.ifGo = false;
                this.cutGo = true;
                this.cutIndex = index;
                this.oldX = e.x;
                this.oldWidth = this.$refs.moveTb.rows[0].clientWidth;
                this.tdOldWidth = this.titleData[this.cutIndex].width;
            },
            //边界经过事件  
            cutSeenter(index){
            },
        },
        data () {
            return {
                ifGo:false,
                cutGo:false,
                tdItem:'',
                oldWidth:0,
                tdOldWidth:0,
                oldX:0,
                cutIndex:-1,
                endIndex:-1,
                starIndex:-1,
                sX:0,
                sY:0,
                // titleData:[
                //     {title:'时间',name:'a',width:180},
                //     {title:'部门',name:'b',width:180},
                //     {title:'币种',name:'c',width:180},
                //     {title:'人数',name:'d',width:180},
                //     {title:'金额',name:'e',width:180},
                //     {title:'组团名称',name:'f',width:298}
                // ],
                // mainData:[
                //     {
                //         a:'时间',
                //         b:'部门',
                //         c:'币种',
                //         d:'人数',
                //         e:'金额',
                //         f:'组团名称',
                //     },
                //     {
                //         a:'时间',
                //         b:'部门',
                //         c:'币种',
                //         d:'人数',
                //         e:'金额',
                //         f:'组团名称',
                //     },
                //     {
                //         a:'时间',
                //         b:'部门',
                //         c:'币种',
                //         d:'人数',
                //         e:'金额',
                //         f:'组团名称',
                //     },
                // ]
            }
        },
    }
</script>

<style lang="less" scoped>
#index{
    width: 1200px;
    float: left;
    margin: 5px 0 20px 0;
    overflow: auto;
    #headTable{
        color: #3C3C3C;
        font-size: 14px;
        text-align: center;
        border-collapse:collapse;
        border: 1px solid #4B98DE;
        td{
            padding: 8px 0;
            cursor: all-scroll;
            border: 1px solid #4B98DE;
            border-bottom:none;
            position: relative;
            .cut{
                height: 33px;
                width: 6px;
                right: -2px;
                top: 0;
                cursor: col-resize;
                position:absolute;
                opacity: 0;
                background: #4B98DE;
            }
            &.table-title{
                background:#C2E8FF;
                padding: 8px 16px;
                text-align: center;
                border: 1px solid #4B98DE;
                &.super{
                    color: #000;
                    background: #94D5FB;
                }
            }
            &.left-red{
                border-left: 4px solid red;
            }
            &.right-red{
                border-right: 4px solid red;
            }
        }
    }
}
#headTable{
    position: relative;
}
.no-select-text {
    user-select: none;
    -moz-user-select: none;
    -webkit-user-select: none;
}
#info {
    text-align: center;
    background:#94D5FB;
    border: 1px solid #4B98DE;
    width:80px;
    height:30px;
    line-height: 30px;
    opacity: 0.9;
    position:absolute;
}
</style>
