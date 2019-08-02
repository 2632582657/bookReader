<template>
    <div class="ebook">
        <TitleBar :ifTitleAndMenuShow="ifTitleAndMenuShow"/>
        <div class="read-wrapper">
            <div id="read"></div>
            <div class="mask">
                <div class="left" @click="prevPage"></div>
                <div class="center" @click="toggleTitleAndMenu"></div>
                <div class="right" @click="nextPage"></div>
            </div>
        </div>
        <MenuBar :ifTitleAndMenuShow="ifTitleAndMenuShow" :fontSizeList="fontSizeList" 
        :defaultFontSize="defaultFontSize" @setFontSize="setFontSize" :themeList="themeList" :defaultTheme="defaultTheme" 
        @setTheme="setTheme" :bookAvailable="bookAvailable" @onProgressChange="onProgressChange"
        :navigation="navigation" @jumpTo="jumpTo" ref="menuBar"/> 
    </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
import Epub from 'epubjs'
const DOWNLOAD_URL='/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
global.ePub=Epub
    export default {
        data(){
           return{
                ifTitleAndMenuShow:false,
                fontSizeList:[
                    {fontSize:12},
                    {fontSize:14},
                    {fontSize:16},
                    {fontSize:18},
                    {fontSize:20},
                    {fontSize:22},
                    {fontSize:24},
                ],
                defaultFontSize:16,
                themeList:[
                    {
                        name:'default',
                        style:{
                            body:{
                                'color':'#000',
                                'background':'#fff'
                            }
                        }
                    },
                    {
                        name:'eye',
                        style:{
                            body:{
                                'color':'#000',
                                'background':'#ceeaba'
                            }
                        }
                    },
                    {
                        name:'night',
                        style:{
                            body:{
                                'color':'#fff',
                                'background':'#000'
                            }
                        }
                    },
                    {
                        name:'gold',
                        style:{
                            body:{
                                'color':'#000',
                                'background':'rgb(241,236,226)'
                            }
                        }
                    },
                ],
                defaultTheme:0,
                //表示图书是否加载完成
                bookAvailable:false,
                navigation:{}
                
           }
        },
        methods:{
            //根据链接跳转到指定位置
            jumpTo(href){
                this.rendition.display(href)
                this.hide() //跳转时隐藏菜单栏和标题栏
            },
            hide(){
                this.ifTitleAndMenuShow=false //隐藏标题栏菜单栏
                this.$refs.menuBar.hideSetting() //隐藏弹出的设置栏
                this.$refs.menuBar.hideContent()   //隐藏目录
            },
            //p 进度条的数值 （0-100）
            onProgressChange(p){
                const percentage=p/100; //将进度条数值换算为百分比
                const location=percentage>0 ? this.locations.cfiFromPercentage(percentage):0  //根据换算的百分比获取当前页数 如果为0就是0页 否则调用cfiFromPercentage方法获取页数
                this.rendition.display(location) //获取页数之后调用display方法传入页数渲染页面
            },
            //改变主题的方法
            setTheme(index){
                this.themes.select(this.themeList[index].name)
                this.defaultTheme=index
            },
            //遍历数组 通过 this.themes.register注册样式
            registerTheme(){
                this.themeList.forEach(theme=>{
                    this.themes.register(theme.name,theme.style)
                })
            },
            //设置字体的方法  子向父传值
            setFontSize(fontSize){
                this.defaultFontSize=fontSize
                if(this.themes){
                    this.themes.fontSize(fontSize+'px')
                }
            },
            //点击center显示菜单
            toggleTitleAndMenu(){
                this.ifTitleAndMenuShow=!this.ifTitleAndMenuShow
                if(!this.ifTitleAndMenuShow){
                    this.$refs.menuBar.hideSetting()
                }
            },
            //点击翻页上一页
            prevPage(){
                //Rendition.prev
                if(this.rendition){
                    this.rendition.prev();
                }
            },
            //点击翻页下一页
            nextPage(){
                //Rendition.next
                if(this.rendition){
                    this.rendition.next();
                }
            },
            //电子书解析和渲染
            showEpub(){
                //1.生成ebook对象
                this.book=new Epub(DOWNLOAD_URL)
                // console.log(this.book)
                //2.生成Rendition对象 通过book.renderTo生成
                this.rendition=this.book.renderTo('read',{   //第一个参数是渲染元素的id 第二的参数是渲染的范围
                //    width: window.innerWidth,
                //     height: window.innerHeight,
                //     // 兼容iOS
                //     method: 'default'
                })
                //3.通过Rendition.display渲染电子书
                this.rendition.display()
                //获取theme对象
                this.themes=this.rendition.themes
                //设置默认字体
                this.setFontSize(this.defaultFontSize)

                //this.themes.register(name,style) 通过这个方法注册上面的样式

                //this.themes.select(name)注册号通过这个方法传入名称选择使用样式
                this.registerTheme()
                //设置默认样式
                this.setTheme(this.defaultTheme)
                //获取locations对象 默认是空的
                //通过epub.js的钩子函数ready实现 表示电子书加载完成
                this.book.ready.then(()=>{
                    //通过钩子函数获取navigation对象 实现目录功能
                    this.navigation=this.book.navigation
                    return this.book.locations.generate()//调用这个方法生成电子书定位服务
                }).then(res=>{
                    this.locations=this.book.locations
                    this.bookAvailable=true
                })
            }
        },
        mounted(){
            this.showEpub()
        },
        components:{
            TitleBar,
            MenuBar
        }
    }
</script>

<style lang='scss' scoped>
    @import 'assets/styles/global.scss';
    .ebook{
        position: relative;
        .read-wrapper{
            .mask{
                position: absolute;
                top: 0;
                left: 0;
                display: flex;
                width: 100%;
                height: 100%;
                z-index: 100;
                .left{
                    flex: 0 0 px2rem(100);
                }
                .center{
                    flex: 1;
                }
                .right{
                    flex: 0 0 px2rem(100);
                }
            }
        }
    }
</style>