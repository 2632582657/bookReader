<template>
    <transition name="slide-right">
        <div class="content">
            <div class="content-wrapper" v-show="bookAvailable">
                <div class="content-item" v-for="(item,i) in navigation.toc" :key="i" @click="jumpTo(item.href)">
                    <span class="text">{{item.label}}</span>
                </div>
            </div>
            <div class="empty" v-show="!bookAvailable">加载中</div>
        </div>
    </transition>
</template>

<script>
    export default {
        props:['ifShowContent','navigation','bookAvailable'],
        methods:{
            jumpTo(href){
                this.$emit('jumpTo',href)
            }
        }
    }
</script>

<style lang='scss' rel="stylesheet/scss" scoped>
    @import '../assets/styles/global';
    .content{
        position: absolute;
        top: 0;
        left: 0;
        z-index: 112;
        width: 80%;
        height: 100%;
        background: white;
        .content-wrapper{
            width: 100%;
            height: 100%;
            overflow: auto;
            .content-item{
                padding: px2rem(10) px2rem(15);
                border-bottom: px2rem(1) solid #ccc;
                .text{
                    font-size: px2rem(14);
                    color: #333;
                }
            }
        }
        .empty {
            width: 100%;
            height: 100%;
            @include center;
            font-size: px2rem(16);
            color: #333;
        }
    }
</style>