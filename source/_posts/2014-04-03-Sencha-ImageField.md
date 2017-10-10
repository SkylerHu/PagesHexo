---
title: Sencha自定义ImageField用于表单中显示图片
date: 2014-04-03 23:26
tags:
  - Sencha
---
苦于sencha源码中

| xtype | Class |
| ---- | ---- |
| textfield | Ext.field.Text |
| numberfield | Ext.field.Number |
| textareafield | Ext.field.TextArea |
| hiddenfield | Ext.field.Hidden |
| radiofield | Ext.field.Radio |
| filefield | Ext.field.File |
| checkboxfield | Ext.field.Checkbox |
| selectfield | Ext.field.Select |
| togglefield | Ext.field.Toggle |
| fieldset | Ext.form.FieldSet |

并没有ImageField，显示图片起来非常麻烦，于是查看源码自定义了一个.

secnha-touch-defineBySelf,js该文件在secha原库和程序入口之间加载即可

```html
<script src="js/sencha-touch-all-2.3.0.js"></script>  
<script src="js/sencha-touch-defineBySelf.js"></script>  
<script src="app.js"></script>
```

secnha-touch-defineBySelf,js内容如下：

```js
/**  
 * @Class Ext.Img.ImageField 
 * @createTime 2014-04-03 
 * 用于表单中的ImageField，通过setValue()来设置图片路径显示图片，通过getValue()来获得图片的路径 
 *  form.getValues()时也会在values中存入图片的相应路径字段 
 *      用法： 
 *          具有一般的Panel的所有属性，并且还多一个一个value属性可以设置图片的路径 
 */  
Ext.define('Ext.field.ImageField', {  
    extend : 'Ext.Panel',  
    xtype : ['imagefield', 'imgfield'],  
      
    isField: true,  
    isFormField: true,  
      
    config : {  
        name : null,  
        value : null,  
        items: [  
        {  
            xtype : 'img',  
            id : 'self-img_photo',  
            width : '100%',  
            height : '100%',  
            margin : '0 0 0 0',  
            style : '{background-repeat:no-repeat; background-size:100% auto; background-position:center;}',  
        },  
        ],  
    },  
      
    setValue : function(path) {  
        Ext.getCmp('self-img_photo').setSrc(path);  
    },  
      
    getValue : function() {  
        return Ext.getCmp('self-img_photo').getSrc();  
    },  
      
});
```
