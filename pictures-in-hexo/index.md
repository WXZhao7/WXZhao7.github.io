# Pictures-in-Hexo


### 写在前面

**这里讨论的方法都以使用Markdown编辑器——Typora能够本地预览且Hexo工作正常为目的,如果擅长js的话建议直接改写插件**

## 总结

**Typora取消全局的上传/复制到相关文件夹设置, 该文件的图片处理方式由文件的Front matter决定, Hexo通过插件`hexo-asset-image`处理图片的路径问题**

## 具体操作

### 1. 安装配置插件

**此处使用修改的`hexo-asset-image`来在Markdown语法中使用<font color=red>相对路径</font>来引入图片**

> 此处给出自己Fork的版本和原作者的地址


```shell
npm install https://github.com/WXZhao7/hexo-asset-image --save
```

```shell
npm install https://github.com/CodeFalling/hexo-asset-image --save
```

### 2. 设置配置文件
#### 2.1 修改主页配置文件`_config.yml`以配合`hexo-asset-image`插件

```
post_asset_folder: true
marked:  
  prependRoot: true  
  postAsset: true
```
####  2.2 修改scaffolds文件`scaffolds/post.md`的front matter
```yaml
---
# draft.md：保存到本地对应文件夹
typora-copy-images-to: {{ title }} 
typora-root-url: {{ title }} 
---
---
# post.md：自动上传文件至图床
typora-copy-images-to: upload 
---
```
#### 2.3 修改Typora的设置

**重点1：优先使用相对路径**

**重点2：允许根据YAML设置自动上传文件**

![Typora设置](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-01-25T22:01:16.685-2021-01-25T22-01-02.668-image-20210125212538212.png)

### 3. 具体效果
#### 3.1 自动创建本地储存图像的文件夹并将插入的图片自动复制到对应的文件夹
`hexo new draft LocalTest`

> copy and paste the screenshot 

![Typora效果图](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-01-26T10:21:18.439-image-20210125214714353.png)
`hexo server`
![Hexo博客效果图](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-01-26T10:21:18.440-image-20210125215533103.png)

#### 3.2 使用PicGo自动上传图片至图床以避免本文的所有问题
`hexo new draft PostTest`
> copy and paste the screenshot 

![Typora效果图](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-01-26T10:21:18.440-image-20210125215328476.png)
`hexo server`

![Hexo博客效果图](https://cdn.jsdelivr.net/gh/WXZhao7/PictureBed/images/2021-01-26T10:21:18.440-image-20210125214842748.png)

###  4. 其他参考方案

#### 4.1 参考此篇博客(尝试失败)

[使Typora可以显示hexo图片](https://ytianxia6.xyz/archives/21)

#### 4.2 参考此篇博客(未尝试)
[记录 Hexo 图片的坑](https://myfavs.win/2019/08/08/%E8%AE%B0%E5%BD%95-Hexo-%E5%9B%BE%E7%89%87%E7%9A%84%E5%9D%91/)
1. 将博客根目录下的文件 _config.yml 中的 post_asset_folder: false 改为 post_asset_folder: true。
2. cmd 在博客根目录下 npm install hexo-asset-image -–save。
3. 将博客根目录下的文件 package.json 中的 "hexo-asset-image": "1.0.0" 修改为 "hexo-asset-image": "0.0.3"，在博客根目录下 cmd npm install；
4. 修改 博客根目录\node_modules\hexo-asset-image\index.js 文件中的第 38 行为 var srcArray = decodeURI(src).split('/').filter(function(elem){；
5. 在博客根目录下 cmd hexo clean && hexo g && hexo s，完成。

**如果依旧不能使用，可以尝试修改`/node_modules/hexo-asset-image/index.js`，并不保证可行性**
