---
title: v-select懒加载
date: 2022-06-20 16:15:22
tags: v-select懒加载
comment: 'valine'
categories: 
- vue

---

# v-select懒加载

### 1.标签代码

```vue
<template>
    <el-select placeholder="请选择" v-model="selectedCode" @change="changeSelected"
    :filter-method="filterMethodHandler" filterable v-el-select-lazy='loadMore'>
        <el-option :label="'请选择'" value=""></el-option>
        <el-option :label="item[labelKey]" :key="item.id" :value="item[labelKey]" v-for="item in expectCodeFilter">
            <span class="fl">{{item[labelKey]}}</span>
            <span class="fl codeName t-overflow" :title="item[labelValue]">({{item[labelValue]}})</span>
        </el-option>
    </el-select>
</template>
```

### 2.vue代码

```vue
<script>
    export default {
        name: "selectLazyLoading",
        directives:{
            'el-select-lazy': {
                bind(el, binding) {
                    const SELECTWRAP_DOM = el.querySelector( '.el-select-dropdown .el-select-dropdown__wrap');
                    SELECTWRAP_DOM.addEventListener('scroll', function() {
                        const condition = this.scrollHeight - this.scrollTop <= this.clientHeight;
                        if (condition) {
                            binding.value();
                        }
                    });
                }
            }
        },
        data(){
            return {
                formData:{
                    pageIndex:1,
                    pageSize:20,
                },
                selectedCode:'',
                allCodeData:[]
            }
        },
        props:{
            selectModel:String,
            labelKey:{
                type:String,
                default:''
            },
            labelValue:{
                type:String,
                default:''
            },
            renderCode:{
                type:Array,
                default:function(){
                    return []
                }
            },
        },
        model:{
            prop:'selectModel',
            event:'selection'
        },
        computed:{
            expectCodeFilter(){
                let num = this.formData.pageIndex * this.formData.pageSize;
                return this.allCodeData.filter((ele,index)=>{
                    return index < num;
                })
            }
        },
        watch: {
            selectModel:{
                handler:function(val) {
                    this.selectedCode = val
                },
                immediate:true
            }
        },
        methods:{
            filterMethodHandler(query){
                //搜索时，从全部的数据中查找
                if(query !== ''){
                    this.allCodeData =  this.renderCode.filter(item =>
                	item[this.labelKey].toLowerCase().indexOf(query.toLowerCase()) > -1)
                } else {
                    //如果搜索框为空，则初始化下拉框
                    this.initSelectData();
                }
            },
            changeSelected(val){
                //获取下拉框选中的值
                let sendEmit = {};
                for (let i = 0; i < this.expectCodeFilter.length; i++) {
                    const item = this.expectCodeFilter[i];
                    if(item[this.labelKey] === val){
                        sendEmit = item;
                        break
                    }
                }
                this.$emit('getChangeValue',sendEmit);
                this.$emit('selection',val);
            },
            loadMore(){
                //下拉框滚动时触发
                this.formData.pageIndex++;
            },
            initSelectData(){
                //初始化搜索下拉框
                this.formData = {
                    pageIndex:1,
                    pageSize:20,
                };
                this.allCodeData = this.renderCode;
            },
        }
    }
</script>
```

