# 仿b站登录验证

#### 介绍
模仿b站的登录滑块验证，我提供了纯css+js的方式以及vue的方式

#### 软件架构
软件架构说明


#### 注意事项
纯css+js写出来的验证滑块直接复制出来用就可以，css代码与js代码和html代码写在了一块，截图如下
- ![输入图片说明](https://images.gitee.com/uploads/images/2020/0313/223801_178e6667_5118695.png "屏幕截图.png")
##### 但是vue版本的验证滑块要注意了。
1、vue版本的验证滑块是在vue脚手架下完成的；
2、在使用过程中，我搭配了elementui来给这个验证滑块加了一个边框，所以小伙伴们使用前不要忘了安装elementui；
3、这个项目的图片是通过java转base64取得的，也可以根据自己的方式更改图片源。验证滑块搭配以前写的项目截图如下。
- ![输入图片说明](https://images.gitee.com/uploads/images/2020/0313/224533_e06462c5_5118695.png "屏幕截图.png")

#### 组件使用案例

```
 <verified :visible="verifiedvisible" @successHandle="getUserInfo()"></verified>
```
 **参数说明** 
|  参数名  |   用途  |默认值   |类型|
| --- | --- | --- | --- |
|  successHandle   | 成功后干啥    |     |方法|
|   visible  |   是否可视  | 默认值：false    |属性 |

