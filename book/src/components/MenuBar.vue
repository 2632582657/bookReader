<template>
    <div class="menu-bar">
        <transition name="slide-up">
            <div class="menu-wrapper" :class="{'hide-box-shadow':ifSettingShow || !ifTitleAndMenuShow }" v-show="ifTitleAndMenuShow">
                <div class="icon-wrapper">
                    <span class="icon-menu icon" @click="showSetting(3)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-progress icon" @click="showSetting(2)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-bright icon" @click="showSetting(1)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-a icon" @click="showSetting(0)">A</span>
                </div>
            </div>
        </transition>
        <transition name="slide-up">
            <div class="setting-wrapper" v-show="ifSettingShow">
                <!-- 字体设置 -->
                <div class="setting-font-size" v-if="showTag==0">
                    <div class="preview" :style="{fontSize:fontSizeList[0].fontSize+'px',justifyContent:'flex-end'}">A</div>
                    <div class="select">
                        <div class="select-wrapper" v-for="(item,i) in fontSizeList" :key="i" @click="setFontSize(item.fontSize)">
                            <div class="line"></div>
                            <div class="point-wrapper">
                                <div class="point" v-show="defaultFontSize===item.fontSize">
                                    <div class="small-point"></div>
                                </div>
                            </div>
                            <div class="line"></div>
                        </div>
                    </div>
                    <div class="preview" :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px',justifyContent:'flex-start'}">A</div>
                </div>
                <!-- 样式设置 -->
                <div class="setting-theme" v-else-if="showTag==1">
                    <div class="setting-theme-item" v-for="(item,i) in themeList" :key="i" @click="setTheme(i)">
                        <div class="preview" :style="{background:item.style.body.background}" :class="{'no-border':item.style.body.background!=='#fff'}"></div>
                        <div class="text" :class="{'selected':i==defaultTheme}">{{item.name}}</div>
                    </div>
                </div>
                <!-- 进度条 -->
                <div class="setting-progress" v-else-if="showTag==2">
                    <div class="progress-wrapper">
                        <input class="progress" type="range" max="100" min="0" step="1" @change="onProgressChange($event.target.value)"
                        
                         @input="onProgressInput($event.target.value)" :value="progress" :disabled="!bookAvailable" ref="progress">
                    </div>
                    <div class="text-wrapper">
                        <span>{{bookAvailable?progress+'%':'加载中...'}}</span>
                    </div>
                </div>
            </div>
        </transition>
        <ContentView :ifShowContent="ifShowContent" v-if="ifShowContent" :navigation="navigation" :bookAvailable="bookAvailable" @jumpTo="jumpTo" />
        <transition name="fade">
            <div class="content-mask" v-show="ifShowContent" @click="hideContent"></div>
        </transition>
    </div>
</template>

<script>
    import ContentView from '@/components/Content'
    export default {
        data(){
            return{
                ifSettingShow:false,
                showTag:0,
                progress:0,
                ifShowContent:false
            }
        },
        methods:{
            jumpTo(href){
                this.$emit('jumpTo',href)
            },
            hideContent(){
                this.ifShowContent=false
            },
            //松手事件
            onProgressChange(p){
                this.$emit('onProgressChange',p)
            },
            //拖动事件
            onProgressInput(p){
                this.progress=p
                this.$refs.progress.style.backgroundSize=`${this.progress}% 100%`
            },

            setTheme(i){
                this.$emit('setTheme',i)
            },
            setFontSize(fontSize){
                this.$emit('setFontSize',fontSize)
            },
            showSetting(tag){
                this.showTag=tag
                if(this.showTag==3){
                    this.ifSettingShow=false;
                    this.ifShowContent=true;
                }else{
                    this.ifSettingShow=true
                }
               
            },
            hideSetting(){
                 this.ifSettingShow=false   
            }

        },
        props:['ifTitleAndMenuShow','fontSizeList','defaultFontSize','themeList','defaultTheme','bookAvailable',"navigation"],
        components:{
            ContentView
        }    
    }
</script>

<style lang='scss' scoped>
    @import '../assets/styles/global.scss';
    .menu-bar{
        .menu-wrapper{
            position: absolute;
            bottom: 0;
            left: 0;
            z-index: 111;
            width: 100%;
            height: px2rem(48); 
            background: white;
            display: flex;
            box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0,0,0.15);
            &.hide-box-shadow{
                box-shadow: none
            }
            .icon-wrapper{
                flex: 1;
                @include center;
                cursor: pointer;
                .icon-progress{
                    font-size: px2rem(22)
                } 
            }
            
        }
        .setting-wrapper{
            z-index: 112;
            position: absolute;
            bottom: px2rem(48);
            left: 0;
            width: 100%;
            height: px2rem(60);
            background: white; 
            box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0,0,0.15);
            .setting-font-size{
                display: flex;
                height: 100%;
                .preview{
                    flex: 0 0 px2rem(40);
                    @include center
                }
                .select{
                    display: flex;
                    flex:1;
                    .select-wrapper{
                        flex:1;
                        display: flex;
                        align-items: center;
                        &:first-child{
                            .line{
                                &:first-child{
                                    border-top: none
                                }
                            }
                        }
                        &:last-child{
                            .line{
                                &:last-child{
                                    border-top: none
                                }
                            }
                        }
                        .line{
                            flex: 1 ;
                            height: 0;
                            border-top: px2rem(1) solid #ccc;
                        }
                        .point-wrapper{
                            flex: 0 0 0;
                            width: 0;
                            height: px2rem(7);
                            border-left: px2rem(1) solid #ccc;
                            position: relative;
                            .point{
                                position: absolute;
                                top: px2rem(-7);
                                left: px2rem(-9);
                                width: px2rem(20);
                                height: px2rem(20);
                                border-radius: 50%;
                                background: #fff;
                                border: px2rem(1) solid #ccc;
                                box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0,0,0.15);
                                @include center;
                                .small-point{
                                    width: px2rem(5);
                                    height: px2rem(5);
                                    background: #000;
                                    border-radius: 50%;

                                }
                            }
                        }
                    }
                }
                
            }
            .setting-theme{
                display: flex;
                height: 100%;
                .setting-theme-item{
                    flex:1;
                    display: flex;
                    flex-direction: column;
                    padding: px2rem(5);
                    box-sizing: border-box;
                    .preview{
                        flex: 1;
                        border: px2rem(1) solid #ccc; 
                        box-sizing: border-box;
                        &.no-border{
                            border: none;
                        }
                    }
                    .text{
                        flex: 0 0 px2rem(30);
                        font-size: px2rem(12);
                        color: #333;
                        align-self: center;
                        color: #ccc;
                        &.selected{
                            color: #333;
                        }
                    }
                }
            }
            .setting-progress{
                position: relative;
                width: 100%;
                height: 100%;
                @include center;
                .progress-wrapper{
                    width: 100%;
                    height: 100%;
                    @include center;
                    padding: 0 px2rem(30);
                    box-sizing: border-box;
                    .progress{
                        width: 100%;
                        -webkit-appearance: none; /*覆盖默认样式 */
                        height: px2rem(2);
                        background: -webkit-linear-gradient(#999,#999) no-repeat, #ddd;
                        background-size: 0 100%;
                        &:focus{
                            outline: none
                        }  
                        &::-webkit-slider-thumb{ //改变默认滑块样式
                            -webkit-appearance: none;
                            height: px2rem(20);
                            width: px2rem(20);
                            border-radius: 50%;
                            background: white;
                            box-shadow: 0 4px 4px 0 rgba(0, 0,0,0.15);
                            border: px2rem(1) solid #ddd
                        }

                    }
                }
                .text-wrapper{
                    position: absolute;
                    bottom: 0;
                    font-size: px2rem(12)
                }
            }
        }
        .content-mask{
            position: absolute;
            top: 0;
            left: 0;
            z-index: 111;
            display: flex;
            width: 100%;
            height: 100%;
            background: rgba(51, 51, 51, 0.8)
        }
    }
    
</style>