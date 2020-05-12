---
title: 关于img的src不变让浏览器重新加载的实现(图形验证码)
draft: false
tags: [FE, 图形验证码]
category: FE
date: '2020-05-12T17:16:42Z'
author: crawford
keywords: 图形验证码重新加载
---

最近做的项目有这么一个需求：根据手机号获取图形验证码后，填写图形验证码后向手机发送短信验证码，如下图：
![图形验证码](/blogimgs/img-reload1.png)
我在几周前实现了这个功能，上线也有一段时间了，自己测试了几次都没啥问题，也是个不太起眼的功能，所以没太过多关注，但是今天用户在使用的时候，跟我反馈图形验证码不能“换一张”，我立马本地测试发现并没问题，线上测试几次也没问题，但是用户那边始终在点击“换一张”之后图片并未更换，我百思不得其解，然后在查阅了一些文章后发现了问题的所在。

## What's the problem??

先看看我原来是如何实现的：

```ts
//...
<Form.Item label="手机号码" hasFeedback>
  {getFieldDecorator("phone", {
    rules: [
      {
        required: true,
        message: "请输入您的手机号码",
      },
      {
        validator: async (rule, value, callback) => {
          if (!value) return;
          const res = await userRequest.checkPhoneDuplicate({
            phone: value,
          });
          if (res.code === 0) {
            callback("用户不存在！");
          } else if (res.code === 20011) {
            callback();
          } else {
            message.error(res.message);
          }
        },
      },
    ],
  })(<Input placeholder="请输入您的手机号码" />)}
</Form.Item>
//...

<Form.Item label="图形验证码">
  <Row gutter={8} className="form-row">
    <Col span={10}>
      {getFieldDecorator("img_code", {
        rules: [{ required: true, message: "请输入验证码！" }],
      })(<Input />)}
    </Col>
    <Col span={14}>
      {getFieldValue("phone") && getFieldValue("phone").length === 11 && (
        <Row>
          <Col span={16}>
            <img
              src={`xxx/v2/api/api/v1/user/register/image/code/${getFieldValue(
                "phone"
              )}`}
              id="img-code"
            />
          </Col>
          <Col span={8}>
            <a
              onClick={() => {
                const phone = getFieldValue("phone");
                setFieldsValue(
                  {
                    phone: " ",
                  },
                  () => setFieldsValue({ phone: phone })
                );
              }}
            >
              换一张
            </a>
          </Col>
        </Row>
      )}
    </Col>
  </Row>
</Form.Item>

// ...
```

我原来的思路是这样：用户输入手机号后，根据输入的手机号，将 api 直接拼接成 url 放入`img`标签的 src 属性中，这样能获取到第一张图片，之后用户点击换一张时，先保存现在的手机号常量`phone`，将表单里的`phone`项的值设为空，然后在`setFieldsValue`函数的回调中，再将表单的`phone`项的值设为之前保存的电话号码，这样触发`img`标签的重新渲染，重新触发请求获取新的图片。这个思路当时在我看来没啥问题，我自己本地测试没问题，部署后测试也没啥问题，除了是用将值设为空在设回原值这种方法着实有点痨，但是当时也没想到更好的办法，也是第一次做这种需求，也就没多管了，事实证明，你永远也逃不过你在写 bug 这件事，今天用户反馈的时候，我思考了半天觉得没啥问题，唯一可能出现问题的地方可能是`setFieldsValue`这个 api 我是不是使用有什么不当，翻看文档以后始终没解决问题，而且令我更疑惑的是为什么我电脑上没问题，别人那里就会出问题。
于是，我把关注点转向了浏览器，但是始终也没找出个究竟，最后我决定，上 google 看看别人对这种需求的实现，看完以后，恍然大悟。

## How did I solve it??

看完一些文章后我发现，对于类似“换一张验证码”这种保持图片 src 不变，重新加载图片的需求，正确的做法是在每次点击重新加载图片时，在 url 的 query 里面，加一段随机数，这样来保证每次`img`里面的 src 是不一样的，但请求的地址不会变，于是我照这种方法改了自己的代码逻辑如下：

```ts
// ...
<Row>
  <Col span={16}>
    <img src={`xxx/v2/api/api/v1/user/register/image/code/${getFieldValue('phone')}`} id="img-code" />
  </Col>
  <Col span={8}>
    <a
      onClick={() => {
        const img = document.getElementById('img-code') as HTMLImageElement
        if (img) {
          img.src = `xxxx/v2/api/api/v1/user/register/image/code/${getFieldValue('phone')}?t=${Math.random()}`
        }
      }}
    >
      换一张
    </a>
  </Col>
</Row>
// ...
```

问题得到了解决，现在的思路是这样:在每次点击换一张时，通过 dom 操作更换`img`的 src 属性，通过`Math.random()`函数每次生成随机数（这里也可以加时间戳），放入 query 里面，这样每次就能获取新的验证码图片

## 为什么要这样做？？

原来，每个浏览器有自己的缓存策略，像`img`标签的这样的请求，如果下一次请求时 src 与上一次是相同的，那浏览器就直接去读取缓存了，不会触发新的网络请求，所以，保证每次 src 都不一样的话，浏览器就不会读取缓存，而是请求新的 src 地址。

那为什么我之前的逻辑，在我电脑上依旧可以实现呢？原来，我的 chrome 开了下面这个东西：
![chrome network 设置](/blogimgs/img-reload2.png)
相信你也跟我一样恍然大悟，开了这个以后，每次`img`的请求都不会去读取缓存，所以表面上似乎是实现了这个需求，但是用户的浏览器并不一定开了这个设置，所以自然点击的时候没有反应

## 总结

嗯，问题解决了，奇怪了知识又增加了（逃
