<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
//dom加载完后动态设置根元素的font-size  页面宽度变化 rem对应的之也会变化
document.addEventListener('DOMContentLoaded',()=>{
  const html=document.querySelector('html');
  //避免页面较大时字体也会变大 给设置fontsize设置一个上限50
  let fontSize=window.innerWidth/10
  fontSize=fontSize>50 ? 50 : fontSize;
  html.style.fontSize=fontSize+'px'
})
</script>

<style>
  /* #app{
    width: 100%;
    height: 100%;
  } */
</style>
