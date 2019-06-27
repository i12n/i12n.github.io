---
layout: post
title: "RBAC 总结"
description: ""
category: web
tags: [ RBAC ]
---

对于一个系统来说，用户的权限管理几乎是不可或缺的功能，通过权限可以保障资源的安全。通俗来讲，权限定义了用户对资源的操作规则，以此达到限制用户行为的目的。例如，文件的读/写权限，可以限制用户能否对文件进行读/写操作。

  <!-- more -->

  ```sh
  # 设置文件的读写权限
  chmod 775 filename
  chmod u=rwx,g=rwx,o=rx filename
  ```

## 权限三要素

- who：权限的拥用者或主体(subject/principal/user/group/role/actor)
- what：权限针对的对象或资源(resource/object/element)
- how：操作 (action/privilege, 包括正向授权与反向授权)

简而言之，权限对 `who 是否可以对 what 进行 how 的访问操作` 进行判断，形成访问资源的安全规则。

## 访问控制模型

访问控制过程：

- 授权过程：产生 Subject 访问 Resource 的Permission 许可规则

- 鉴权过程：通过 Permission 控制 Subject 对 Resource 可否访问

![access-control.png](/assets/images/web/rbac/access-control.png)

访问控制模型：

- 访问控制列表 ACL (Access Control List)
- 自主访问控制 DAC (Discretionary Access Control)
- 强制访问控制 MAC (Mandatory Access Control)
- 基于角色的访问控制 RBAC (Role Based Access Control)
- 基于属性的权限控制 ABAC (Attribute-Based Access Control)

## ACL 访问控制列表

示例：

User | Read  |  Write | Execute |
-|-|-|-
User1 | YES | YES | YES |
User2 | YES | YES | YES |
User3 | NO | NO |  NO |

说明：

- 需要对每个用户都要维护一个权限列表。

## DAC 自主访问控制

在 ACL 基础上支持了授权操作，某个用户拥有某个权限，该用户可以把这个权限授权给另外一个用户。

例如，User1 可以将 Read 权限授权给 User3：

User | Read  |  Write | Execute |
-|-|-|-
User1 | YES | YES | YES |
User2 | YES | YES | YES |
User3 | YES | NO |  NO |

## MAC 强制访问控制

用户和资源需要双向控制。

例如：
  用户等级：A/B/C
  文件等级：0/1/2/3

0 级文件 允许被 A/B/C 级用户访问
1 级文件 允许被 A/B/C 级用户访问
2 级文件 允许被 B/C 级用户访问
3 级文件 允许被 C 级用户访问

A 等级用户可以访问 <=1 级文件
B 等级用户可以访问 <=2 级文件
C 等级用户可以访问 <=3 级文件

用户能否访问某个文件，是由用户等级和文件等级同时决定的。

## RBAC 基于角色的访问控制

角色关联权限
Role | Read  |  Write | Execute |
-|-|-|-
Role1 | YES | YES | YES |
Role2 | YES | YES | NO |
Role3 | YES | NO |  NO |

用户关联角色
User | Role1  |  Role2 | Role3 |
-|:-|-|-
User1 | YES | NO | NO |
User2 | YES | YES | NO |
User3 | YES | NO |  NO |

用户权限列表

User | Permissions|
-|:-|-
User1 | Read, Write, Execute |
User2 | Read, Write, Execute |
User3 | Read |

用户的权限是由用户拥有的角色决定的，每个角色与多个权限关联。

## ABAC 基于属性的权限控制

略

## RBAC 设计实现

### 用户/角色/权限关系

![rbac](/assets/images/web/rbac/rbac.png)

- **用户** 用户登陆，访问控制，用户管理等功能
- **角色** 角色管理
- **权限** 权限管理

### 用户/角色/权限流程

![rbac-sequence.png](/assets/images/web/rbac/rbac-sequence.png)

1. 用户登陆成功返回用户 Token
2. 通过 Token 获取角色/权限等用户信息
3. 页面根据权限展现内容
4. 接口根据权限返回数据

注意事项：

- 后端会将 Token 与 用户信息做数据缓存，因为与登陆操作相比，获取用户信息的请求会更为频繁。
- 由于缓存的缘故，当权限发生变动时，可能会存在权限发生变动，而不立即生效的情况。
- Token 失效之后需要再次登陆。

### 前端权限控制

![auth.png](/assets/images/web/rbac/auth.png)

1. 页面/路由级别 (数据可读权限)
    - 页面不可见
2. 数据/内容级别 (数据可读权限)
    - 内容不可见
3. 按钮/操作级别 (数据可写权限)
    - 按钮可见，不可操作
    - 按钮不可见

  无权限不可见，代码实例：

  ```js
  function authorize(authorities = [], authId = '') {
    if (authId && authorities.includes(authId)) {
      return component => component
    }
    return () => null
  };

  @connect(({ user }) => ({account: user}))
  class Authorize extends React.Component {
    static propTypes = {
      authId: PropTypes.string.isRequired,
    };

    render() {
      const { authId, account = {}, children } = this.props;
      const { authorities = [] } = account;
      return authorize(authorities, authId)(children);
    }
  }

  <Authorize authId={1}>
    <button>编辑</button>
  </Authorize>

  ```

### 数据权限控制

  需要，后端通过 Token 做权限控制

### 权限树与权限列表

![auth-tree.png](/assets/images/web/rbac/auth-tree.png)

权限的是一个列表，某些时候以树的形式展现。

### 一次授权（临时授权）

用户 A 没有某个权限，用户 B 拥有某个权限；B 可以授权给 A 这个权限，使 A 拥有临时权限（次数/时效限制）。

- B 用户登陆，得到 临时 Token
- A 用户获得临时权限
- A 用户操作
- A 用户取消临时权限，注销 临时 Token

### 场景问题

1. 总部/大区: 总部/大区是公司的两种组织类型，不同类型的组织的用户进入系统使用的功能不同。针对这种情况需要引入组织树的概念，这种数据可视权限应该由组织类型决定，比如：上级组织职能看到下级组织员工负责的数据，而不能看到其他平级组织及其下级组织的员工数据等
2. 普通编辑者/高级编辑者：普通编辑者/高级编辑者是公司的两种角色，高级编辑者能够进行审核，高级编辑者能够看到更多的页面内容。高级编辑者是一种角色，可见性应该由权限控制。
3. 尽量避免多项权限的或/与关系


## 参考资料
[https://security.stackexchange.com/questions/346/what-is-the-difference-between-rbac-and-dac-acl](https://security.stackexchange.com/questions/346/what-is-the-difference-between-rbac-and-dac-acl)

[http://www.manasclerk.com/blog/2003/09/30/role-based-access-control-vs-acls/](http://www.manasclerk.com/blog/2003/09/30/role-based-access-control-vs-acls/)
[https://dinolai.com/notes/others/authorization-models-acl-dac-mac-rbac-abac.html](https://dinolai.com/notes/others/authorization-models-acl-dac-mac-rbac-abac.html)

[https://dinolai.com/notes/others/authorization-models-acl-dac-mac-rbac-abac.html](https://dinolai.com/notes/others/authorization-models-acl-dac-mac-rbac-abac.html)

[http://www.woshipm.com/pd/872372.html](http://www.woshipm.com/pd/872372.html)

[AmazonS3 acl](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html)
