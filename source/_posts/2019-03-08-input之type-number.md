---
title: input之type=number
tags: [移动端, input, type="number"]
toc: true
mathjax: true
date: 2019-03-08 14:51:56
categories:
- 移动端开发
- 问题积累
---

# 业务需求

页面中存在输入框，点击输入框需要弹出数字键盘，且只能输入合法数字

# 解决方案

1. 自己开发一个键盘组件
2. input type="number"

# input type="number"存在的问题（只调查了部分机型，不一定完全准确）

| 机型 | 键盘类型 | 表现 | 输入限制 |
| --- | --- | --- | --- |
| ios | 自带键盘 | 调起带数字的特殊符号键盘，可以切换成英文键盘 | 可以输入任何字符，输入的不是合法的数字，则取到的value为空 |
| ios | 非自带键盘	 | 调起数字键盘，可以切换成英文键盘 | 可以输入任何字符，输入的不是合法的数字，则取到的value为空 |
| android | 自带键盘 | 调起纯数字键盘，可以切换成英文键盘 | 可以输入数字，-、小数点，输入的不是合法的数字时，取到的value为空 |
| android | 非自带键盘 | 调起纯数字键盘，可以切换成英文键盘 | 可以输入数字，-、小数点和e，输入的不是合法的数字或带有e时，取到的value为空 |

由上表可知，尽管input设置了type="number"，但是在不同的机型与输入法下，所弹出的键盘和能输入的字符是不同的，并不能保证用户输入的是合法的数字，所以我们要对用户的输入进行限制。通过监听input事件，在用户进行输入后，进行正则校验替换非法字符，但是设置了type="number"后，如果用户输入的不是合法的数字，取到的value为空，根本无法进行合法性校验与替换操作，在Android下还好，它已经帮我们限制了英文、符号等非法字符的输入，用户的输入得到了一定的保证，我们可以在input的blur事件中进行最终的合法性校验，并给予用户一定的提示。但是在ios下，用户可以输入任意的字符，也就是说用户输入非法的字符的可能性提高了很多，若同样在blur事件中进行校验与替换操作，给用户带来的体验过差，所以在ios下使用type="number"并不可行

# 那么在ios下是否有其它调起纯数字键盘的方法

答案是肯定的，以下两种方法可以调起纯数字键盘，但是调起来的数字键盘是不带小数点的，如果需要输入小数的话，这种方法是不可行的

1. type="tel"
2. pattern="[0-9]*"
3. 如果是webview，调用native的事件貌似可以，但需要native人员的支持

# 思考

综上所诉，type设置为number的方法是不可行的，无法实现我们对用户的输入进行实时的控制，但是把type设置为text，每次调起的都是英文键盘，用户还得切换一下键盘，才能愉快的进行输入，这个体验实在是太糟糕了，如果你的页面只有一个输入框，用户或许还能勉为其难的接受，但是如果存在多个，每次用户进行输入都得切换以下，可能砸手机的心态都有了😡，可见，把type设置为text的方法更加不可行

# 回到我们的需求

1. 弹出数字键盘
2. 限制用户输入

首先要满足弹出数字键盘，就必须设置type="number"，但是设置了type="number"之后，该怎么限制用户的输入呢？把type设回text就行了，于是有了以下处理方案：

````````````````javascript
<template>
    <input type="number" :model="price" @focus="focusHandler($event)" @blur="blurHandler($event)"/>
</template>
<script>
    export default {
        watch: {
            price: function () {
                const newPrice = // 正则替换操作
                return newPrice
            }
        },
        methods: {
            blurHandler (e) {
                e.target.type = 'number'
            },
            focusHandler (e) {
                setTimeOut(() => {
                    e.target.type = 'text'
                }, 100)
            }
        }
    }
</script>
````````````````

在输入框获取焦点，弹起了数字键盘后，再把input的type设回text，这样当用户输入的是非数字字符时，也能获取到正常的value值了，然后进行合法性校验和替换，在失去焦点的时候，再把type设回number，以防下次点击的时候弹出非数字键盘，经过测试这种实现方法在ios下操作正常，但是在android下根本无法弹出键盘，于是有了下面的解决方案

# 最终方案

`````````````javascript
<template>
    <div class="demo">
        <input type="number" class="demo--input"
        	v-model="realValue" 
   			@focus="inputFocusHandler($event)"
        	@blur="inputBlurHandler($event)"
        	@input="inputHandler">
    </div>
</template>

<script>

export default {
    name: "DemoInput",
    props: {
   },
    data() {
        return {
            realValue: ""
        }
    },
    computed: {
    },
    created() {
    },
    watch: {
        realValue: function(newValue) {
            let realValue = newValue.replace(/[^0-9.]|(^\.)/g, "")
            if (realValue.charAt(realValue.length - 1) === ".") {
                let tempValue = realValue.slice(0, -1)
                this.realValue = /\./g.test(tempValue) ? tempValue : realValue
                return
            }
            this.realValue = realValue
        }
    },
    methods: {
        validateValue () {
            const { realValue } = this
            // 校验输入的是否是数字
            if (
                realValue !== "" &&
                !/^[0-9]+(\.[0-9]+)?$/.test(realValue)
            ) {
                this.realValue = ""
                alet('请输入正确的数字！')
                return false
            }

            // 去除数字前面多余的0
            if ((/\./g).test(realValue)) {
                this.realValue = realValue.replace(/^0+\./, "0.")
            } else {
                this.realValue = realValue.replace(/^0+/, "")
            }

            return true
        },
        inputBlurHandler(e) {
            if (window.APP_TYPE === "ios") {
                e.target.type = "number"
            }
            this.validateValue()
        },
        inputHandler(e) {
        	// 长度输入限制
            if (this.realValue.length > 7) {
                this.realValue = this.realValue.slice(0, 7)
            }
        },
        inputFocusHandler(e) {
            if (window.APP_TYPE === "ios") {
                setTimeout(() => {
                    e.target.type = "text"
                }, 100)
            }
        }
    }
}
</script>

<style lang="scss" scoped>
</style>


`````````````

# Tip

1. 移动端自定义指令v-focus仍然无法实现点击某个按钮，使input自动获取焦点，在pc端可行