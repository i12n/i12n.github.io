---
layout: post
title: "serverless"
description: ""
category: web
tags: [ serverless ]
---

云计算的发展在经历了 IaaS（Infrastructure as a Service － 基础设施即服务），PaaS（Platform as a Service － 平台即服务），SaaS（Software as a Service－软件即服务）几个阶段后，Serverless（无服务器化）趋势越发明显。

<!-- more -->

## 简介

Serverless 是指在构建和运行应用时无需管理服务器让开发人员更专注于编写应用程序的业务逻辑。它描述了一个更细粒度的部署模型，在该模型中，应用被拆解为一个或多个细粒度的函数被上传到一个平台，然后根据当前所需执行、扩展、计费等。平台方将服务器以服务化的方式提供给业务开发人员，平台方要对其提供的服务完全负责，它的使用者只需对服务按需计费即可。

![Cloud Infrastructure with BaaS and FaaS](/assets/images/serverless/serverless-infrastructure.png)

  > "Everyone wants just to focus on business logic."

Serverless 平台在 PaaS 的基础上提供了以下两种服务：FaaS（Functions as a Service - 函数即服务）和 BaaS（Backend as a Service - 后端即服务）:

- FaaS（Functions as a Service - 函数即服务），通常提供事件驱动（event-driving）的计算。开发人员使用由事件（event）或 HTTP 请求触发的函数运行和管理应用程序代码。开发人员将小的代码单元部署到 FaaS，FaaS 按需执行和扩展，开发人员无需管理服务器或任何其他底层基础设施。

  > 提供颗粒度更细的计算服务
  > 编写函数实现业务逻辑
  > 云资源的弹性使用

- BaaS（Backend as a Service - 后端即服务），是第三方基于 API 的服务，用于替换应用程序中的核心功能子集。因为这些 API 是作为一个自动扩缩容和透明操作的服务提供的，所以开发人员认为这是无服务器的

  > 提供基础的后端服务
  > 通过 SDK 使用相关服务
  > 为业务实现提供底层支持

通常认为 Serverless = FaaS + BaaS. 函数在 FaaS 平台中，需要通过一系列的事件驱动函数执行。每次函数执行，可能使用的都是不同的容器，无法进行内存或数据共享，因此是无状态的。BaaS 提供的后端服务，开发者可以用最简单的方式一键集成中间件、存储、消息等能力，用最简单的方式让业务快速落地。

![Serverless Computing](/assets/images/serverless/serverless-computing.png)

Serverless 工作过程

1. 开发者编写业务逻辑代码
2. 代码部署到云平台
3. 用户的请求触发了事件
4. 事件对应的函数载入容器
5. 执行函数结果响应给用户

  ![Serverless Procedure.png](/assets/images/serverless/serverless-procedure.png)

Serverless 价值

- NoOps：平台提供统一的运营支持，业务方几乎不需要运营投入
- 按需付费：只需为使用的资源付费，空闲资源不需要付费
- 聚焦业务：开发人员的更多精力用于实现应用的业务逻辑，不需关注应用依赖的服务

## 前端场景

Serverless 降低了接入服务的门槛，通常搭建 Node Server 需要服务器管理和运营方面的经验，而这些恰好是前端工程师所不擅长的，Serverless 让前端工程师更容易地使用服务，未来前端很可能会打破当前的边界，探索出更多的可能性。

### 开发模式

Serverless 降低了后端开发的门槛。以往的后端应用被拆分为一个个函数，平台提供了各种服务的 SDK 方便在应用中使用，让开发人员聚焦于业务，前端工程师可以完成整个业务的开发工作。

![serverless-application-development](/assets/images/serverless/serverless-application-development.png)

开发模式的转变也会导致前端职能的转变，由前端开发转变为应用开发，完成整个业务链路的开发工作。这其中减少了前后端联调/服务器运维等环节，业务开发会更加高效。

### BFF

随着业务复杂程度的增加以及微服务的不断演进，为了提高接口的可复用性，服务端的接口会进行通用化设计。这就导致前端不能直接使用数据，需要将数据进行加工处理，以满足业务的 UI 渲染需求。因此，通常需要 BFF (Backend For Frontend) 作为适配层做一些业务逻辑处理，BFF 适用于多端应用/聚合裁剪等场景。

![node-bff](/assets/images/serverless/node-bff.png)

通常 BFF 层由前端 Node 服务负责，对后端的原子化数据进行聚合和剪裁等处理，最终提供给 UI 渲染。但是 BFF 也带来了一些问题：

- 运维成本增加，对于一些高并发的业务场景，并发压力会集中于 BFF 层，这对前端的运维能力有了更高的要求。

- 资源利用率低，在一些低频的业务场景，BFF 层的服务资源很可能长期处于闲置状态。

- 可服用性差，多个业务平台之间的 BFF 层是无法复用的，同样的中间件各个平台都需要重复接入，同样的功能也会在不同平台之间实现。

Serverless 实现的 BFF，一体化解决方案的方式，将运维的压力转给 FaaS 服务，对于业务方无需考虑运维能力。通过网关实现线下、预发、日常环境的管理。BaaS 提供多种基础服务，方便各个业务方使用。

![node-bff](/assets/images/serverless/serverless-bff.png)

### 服务端渲染

SSR 服务端渲染在减少白屏时间等方面有着很大的优势。通常服务端渲染是以路由为单位进行的，当用户请求页面时，服务端将请求解析为对应的路由，通过路由将视图解析成 HTML 文档，返回给客户端用户。服务端渲染会带来额外的运维成本，前端需要维护用于渲染的服务。

![server-render](/assets/images/serverless/server-render.png)

基于 Serverless 的服务端渲染，以函数为单位提供渲染页面 UI 的能力，将解析好的 HTML 吐给用户端。由之前的路由解析，拆解为函数解析，并且减少了运维成本。

![serverless-faas-render](/assets/images/serverless/serverless-faas-render.png)

目前，组件化开发方式在前端以及有了广泛的应用，而组件通常就是一个函数，例如 React 就是一个 `UI = f(states)` 的框架，与 Serverless 的函数理念一致。 进而可以将 FaaS 延伸到 CaaF（组件即函数），提供组件维度的渲染服务，一个页面场景就是多个组件微服务的聚合。

![serverless-caaf-render](/assets/images/serverless/serverless-caaf-render.png)

### 场景应用

#### 小程序

Serverless 很好的一个应用场景就是小程序的云开发功能，支付宝小程序和微信小程序都支持这一功能。云开发功能为移动开发者提供的一站式后端云服务，无需搭建服务器，即可使用云端能力。它帮助开发者统一构建和管理资源，免去了移动应用开发过程中繁琐的服务器搭建及运维、域名注册及备案、数据接口实现等繁琐流程，让开发者可以专注于业务逻辑的实现，业务逻辑的实现可以由前端开发完成。

![serverless-minapp](/assets/images/serverless/serverless-minapp.png)

#### Web IDE

开发人员的不仅仅要开发业务代码，还要考虑不同运行环境的部署和维护等问题。Web IDE 的出现，打通了云上环境，屏蔽开发环境，支持调试环境，一站式编、编译、部署代码。另外，云平台将后端封装为了 BaaS 服务，前端通过 SDK 可以完成后端业务逻辑。

## 参考资料

- https://www.thoughtworks.com/insights/blog/bff-soundcloud
- https://helpcdn.aliyun.com/document_detail/65565.html
- https://cryptiot.de/cloud-computing/serverless-computing-1/
- https://specify.io/concepts/serverless-baas-faas
- https://aws.amazon.com/cn/blogs/china/iaas-faas-serverless/
- https://www.cloudflare.com/learning/serverless/glossary/backend-as-a-service-baas/
- https://martinfowler.com/articles/serverless.html
- https://www.thoughtworks.com/insights/blog/bff-soundcloud
- http://www.alloyteam.com/2018/09/13430
- https://github.com/nodejh/nodejh.github.io/issues/49
- https://mp.weixin.qq.com/s/wMA2gS4QWhCft6dS7zJvTQ