# 小程序教程
##     小小白入门
-     微信公众平台 下载小程序开发工具
-     注册小程序账号
-     登陆后新建项目打开IDE
-     开发工具推荐：vscode - （这次直接使用微信开发者工具）装好后等我讲
-     1.HTML https://www.w3school.com.cn/html/html_layout.asp
-     2 CSS https://www.w3school.com.cn/css/index.asp

## 非新手入门
-     微信社区的开发者文档 https://developers.weixin.qq.com/miniprogram/dev/framework/
-     微信小程序之组件，API，框架 直接莽 
-     什么是API？https://www.zhihu.com/question/21430743/answer/32541089
-     什么是组件？组件化开发的优势？https://zhuanlan.zhihu.com/p/75024207，https://www.zhihu.com/question/29735633/answer/90873592
-     **高内聚，低耦合**
-     软件文档编制：**Markdown** markdown是什么？有什么好的markdown编辑器 有道云笔记挺方便的
-     什么是小程序云开发 https://developers.weixin.qq.com/miniprogram/dev/wxcloud/basis/capabilities.html
-     前端和后端 前端-> 后端 api，
-     js ES6语法规则 http://jsrun.pro/tutorial/cZKKp
-     好看的颜色代码 https://blog.csdn.net/jockerscolor/article/details/69255346

---
- swiper组件
- Page页面的生命周期
- 数据绑定
- 数据绑定的运算和逻辑
- AppData区域的主要用法
- 事件与事件对象
- 缓存
- 列表渲染
- **Template模板的使用**
- wx:if,wx:for
- 捕捉与回调
- bind和catch
  -绝对路径和相对路径的区别 https://www.cnblogs.com/wangenxian/p/10828276.html





---
小程序云开发项目 ——仿QQ音乐/网易云音乐小程序 

目录
---
- 1.云开发初始化以及轮播图组件复习
- 2.详解wx：key 详解promise 详解async await
- 3.播放列表功能实现
- 4.播放器功能实现
- 5.发现功能实现
- 6.评论与分享功能实现
- 7.我的功能实现
- 8.性能优化
---
第四，五节课内容：

定时触发器
request-promise 安装
windows node环境安装 https://www.cnblogs.com/zhouyu2017/p/6485265.html

playlist组件的js源码，难点在于歌单去重的写法
```
// 云函数入口文件
const cloud = require('wx-server-sdk')

cloud.init()

const db = cloud.database()

const rp = require('request-promise')

const URL = 'http://musicapi.xiecheng.live/personalized'

const playlistCollection = db.collection('playlist')

const MAX_LIMIT = 100

// 云函数入口函数
exports.main = async(event, context) => {
  // const list = await playlistCollection.get()
  const countResult = await playlistCollection.count()
  const total = countResult.total
  const batchTimes = Math.ceil(total / MAX_LIMIT)
  const tasks = []
  for (let i = 0; i < batchTimes; i++) {
    let promise = playlistCollection.skip(i * MAX_LIMIT).limit(MAX_LIMIT).get()
    tasks.push(promise)
  }
  let list = {
    data: []
  }
  if (tasks.length > 0) {
    list = (await Promise.all(tasks)).reduce((acc, cur) => {
      return {
        data: acc.data.concat(cur.data)
      }
    })
  }

  const playlist = await rp(URL).then((res) => {
    return JSON.parse(res).result
  })

  const newData = []
  for (let i = 0, len1 = playlist.length; i < len1; i++) {
    let flag = true
    for (let j = 0, len2 = list.data.length; j < len2; j++) {
      if (playlist[i].id === list.data[j].id) {
        flag = false
        break
      }
    }
    if (flag) {
      newData.push(playlist[i])
    }
  }

  for (let i = 0, len = newData.length; i < len; i++) {
    await playlistCollection.add({
      data: {
        ...newData[i],
        createTime: db.serverDate(),
      }
    }).then((res) => {
      console.log('插入成功')
    }).catch((err) => {
      console.error('插入失败')
    })
  }

  return newData.length
}
```

---
第六节课内容：
---

-      koa洋葱模型https://segmentfault.com/a/1190000013981513?utm_source=tag-newest
-      云函数路由思维导图 https://www.processon.com/view/link/5d873095e4b016b3d5c68548
-      tcb-router 插件安装地址 