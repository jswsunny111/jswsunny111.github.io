---
title: 表单校验方式
date: 2021-07-09 21:27:28
tags: 校验方式
category: Element UI
---

## 实现效果

> 日常总结常规的表单校验方式总结，针对于 element ui 组件库

## 主要

> form 表单内 form-item prop 校验

-   required 必填
-   trigger 触发条件
-   message 警示语
-   type 校验类型
-   pattern 正则校验
-   validator 自定义 校验规则

### 实例代码

```
 const validateDate = (rule, value, callback) => {
      if (value) {
        let selectTime = new Date(value).valueOf();
        let activeTime = new Date().valueOf();
        if (activeTime > selectTime) {
          callback(new Error(`生效时间不能低于或等于当前时间`));
        } else {
          callback();
        }
      } else {
        callback();
      }

 productTitle: [
          { required: true, message: "请输入商品名称", trigger: "blur" },
          { validator: validateDate, trigger: ["blur", "change"] },
          { type: "number", message: "类型应为数字值", trigger: "blur" },
          {pattern: /^\+?[1-9][0-9]*$/,message: '推荐顺序应为正整数',trigger: 'blur',},
        ],
```

### 有依赖数据校验判断

```
computed: {
        // 表单校验规则 在计算属性中可将值进行判断
        rules(){
            return {
                product:[{ required: this.form.big ? true : false, message: "请输入商品名称", trigger: "blur" }]
            };
        }
    },
```

### 自定义正则方式修改输入内容

```
 <el-form-item v-if="showPrice" prop="itemPrice" label="面值">
            <el-input
              v-model="addForm.itemPrice"
              @keyup.native="InputNumber('itemPrice')"
              maxlength="6"
              size="blur"
            ></el-input>
 </el-form-item>

   // 验证只能输入数字
    limitInputNumber (val) {
      if (val) {
        return String(val).replace(/\D/g, '')
      }
      return val
    },
     // 限制只能输入数字(可以输入两位小数)
    limitInputPointNumber(val, parent, property) {
      let tmap = ''
      if (val === 0 || val === '0' || val === '') {
        tmap = ''
      } else {
        let value = null
        value = String(val).replace(/[^\d.]/g, '') // 清除“数字”和“.”以外的字符
        value = value.replace(/^\./g, '0.') // 第一个. 添加0.
        value = value.replace(/\.{2,}/g, '.') // 只保留第一个. 清除多余的
        value = value
          .replace('.', '$#$')
          .replace(/\./g, '')
          .replace('$#$', '.')
        value = value.replace(/^(-)*(\d+)\.(\d\d).*$/, '$1$2.$3') // 只能输入两个小数
        tmap = Number(value)
      }
      this[parent][property] = tmap
    },
     InputNumber(property) {
     this.addForm[property] = this.limitInputNumber(this.addForm[property])
   },
```

### 嵌套验证(联动验证)

```
<!-- region是一个对象,国家和城市是它的属性 -->
  <el-form-item label="活动区域" prop="region">
    <el-select v-model="registData.region.country" placeholder="请选择国家">
      <el-option label="国家一" value="USA"></el-option>
      <el-option label="国家二" value="China"></el-option>
    </el-select>
    <el-select v-model="registData.region.city" placeholder="请选择城市">
      <el-option label="城市一" value="shanghai"></el-option>
      <el-option label="城市二" value="beijing"></el-option>
    </el-select>
  </el-form-item>

  region: [
    {
      type: 'object',
      required: true,
      // 这里有两种处理,一种是自定义验证,拿到值后自己对属性进行验证,比较麻烦
      // validator: (rule, value, callback) => {
      //   console.log(55, value)
      //   if (!value.country) {
      //     callback(new Error('请选择国家'))
      //   } else if (!value.city) {
      //     callback(new Error('请选择城市'))
      //   } else {
      //     callback()
      //   }
      // },
      trigger: 'change',
      // 官网提供了对象的嵌套验证,只需要把需要验证的属性,放在fields对象里,就会按顺序进行验证
      fields: {
        country: {required: true, message: '请选择国家', trigger: 'blur'},
        city: {required: true, message: '请选择城市', trigger: 'blur'}
      }
    }
  ],
```
