---
title: 在 vue 中使用 svg 的方法
date: 2021-06-27 16:33:32
tags: vue
categories: 经验总结
---
{% title h2, 使用方式 %}
{% p primary, 全局引入 %}

```
npm install svg-sprite-loader -S
```
{% p primary, 配置vue.config.js %}

```
// vue.config.js
  chainWebpack: (config) => {
    const svgRule = config.module.rule("svg");
    svgRule.uses.clear();
    svgRule
      .use("svg-sprite-loader")
      .loader("svg-sprite-loader")
      .options({
        symbolId: "icon-[name]",
        include: ["./src/icons"]
      });
  },
```
{% p primary, 制作组件SvgIcon %}

```
<template>
  <svg :class="svgClass" aria-hidden="true">
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
import { computed, ref } from "@vue/composition-api";
// 解析svg
const req = require.context("@/assets/svg", false, /\.svg$/);
const requireAll = (requireContext) => {
  return requireContext.keys().map(requireContext);
};
requireAll(req);

// require.context(‘./svg’, false, /\.svg$/) 参数说明：
// 第一个：目录
// 第二个：是否遍历子级目录
// 第三个：定义遍历文件规则

export default {
  name: "SvgIcon",
  props: ["iconClass", "className"],
  setup(props, { root }) {
    // 计算属性，得出最终结果并返回
    const iconName = computed(() => `#icon-${props.iconClass}`);
    const svgClass = computed(() => {
      if (props.className) return `svg-icon ${props.className}`;
      return "svg-icon";
    });
    return {
      iconName,
      svgClass,
    };
  },
};
</script>

<style lang="scss">
  .svg-icon {
    width: 1em;
    height: 1em;
    // 添加fill后可修改color
    fill: currentColor;
    color: white;
  }
</style>
```
{% p primary, 引入组件 %}

```
<svg-icon :iconClass="iconClass" :className="className"/>
```

