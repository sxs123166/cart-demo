<template>
  <div class="header-container">{{ title }}</div>
</template>

<script>
export default {
props: {
    //声明title自定义属性，允许使用者自定义标题的内容
    default: '',
    type: String
}
}
</script>

<style lang="less" scoped>
.header-container {
    font-size: 12px;
    height: 45px;
    width: 100%;
    background-color: lightblue;
}
</style>