jCasbin
====

[![codebeat badge](https://codebeat.co/badges/c17c9ee1-da42-4db3-8047-9574ad2b23b1)](https://codebeat.co/projects/github-com-casbin-jcasbin-master)
[![Build Status](https://travis-ci.org/casbin/jcasbin.svg?branch=master)](https://travis-ci.org/casbin/jcasbin)
[![Coverage Status](https://coveralls.io/repos/github/casbin/jcasbin/badge.svg?branch=master)](https://coveralls.io/github/casbin/jcasbin?branch=master)
[![Javadocs](https://www.javadoc.io/badge/org.casbin/jcasbin.svg)](https://www.javadoc.io/doc/org.casbin/jcasbin)
[![Maven Central](https://img.shields.io/maven-central/v/org.casbin/jcasbin.svg)](https://mvnrepository.com/artifact/org.casbin/jcasbin/latest)
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/casbin/lobby)

**News**: 仍然担心如何正确的编写jCasbin策略? ``Casbin online editor`` 来了! 尝试: http://casbin.org/editor/

![casbin Logo](casbin-logo.png)

jCasbin是一个强大和有效的开源Java项目的访问控制库. 为基于多种访问控制模型的授权提供了支持(https://en.wikipedia.org/wiki/Computer_security_model).

## Casbin支持的语言:

[![golang](https://casbin.org/docs/assets/langs/golang.png)](https://github.com/casbin/casbin) | [![java](https://casbin.org/docs/assets/langs/java.png)](https://github.com/casbin/jcasbin) | [![nodejs](https://casbin.org/docs/assets/langs/nodejs.png)](https://github.com/casbin/node-casbin) | [![php](https://casbin.org/docs/assets/langs/php.png)](https://github.com/php-casbin/php-casbin)
----|----|----|----
[Casbin](https://github.com/casbin/casbin) | [jCasbin](https://github.com/casbin/jcasbin) | [node-Casbin](https://github.com/casbin/node-casbin) | [PHP-Casbin](https://github.com/php-casbin/php-casbin)
production-ready | production-ready | production-ready | production-ready

## Table of contents

- [支持模型](#支持模型)
- [工作原理](#工作原理)
- [特性](#特性)
- [安装](#安装)
- [文档](#文档)
- [在线编辑器](#在线编辑器)
- [教程](#教程)
- [Get started](#get-started)
- [策略管理](#策略管理)
- [策略持久化](#策略持久化)
- [角色管理](#角色管理)
- [样例](#样例)
- [采纳者](#采纳者)

## 支持模型

1. [**ACL (Access Control List)**](https://en.wikipedia.org/wiki/Access_control_list)
2. **ACL with [superuser](https://en.wikipedia.org/wiki/Superuser)**
3. **ACL without users**: 对于没有认证或用户登录的系统特别有用.
3. **ACL without resources**: 一些场景可能目标是一类资源而不是一个单独的资源，通过使用权限如``write-article``, ``read-log``. 而不会控制一个文件的访问.
4. **[RBAC (Role-Based Access Control)](https://en.wikipedia.org/wiki/Role-based_access_control)**
5. **RBAC with resource roles**: 用户和角色都可以同时拥有角色 (或者是组).
6. **RBAC with domains/tenants**: 用户在不同的域/租户可以有不同的角色.
7. **[ABAC (Attribute-Based Access Control)](https://en.wikipedia.org/wiki/Attribute-Based_Access_Control)**: 语法糖如``resource.Owner``可以用来获取一个资源的属性.
8. **[RESTful](https://en.wikipedia.org/wiki/Representational_state_transfer)**: 支持形如``/res/*``, ``/res/:id``的路径和形如``GET``, ``POST``, ``PUT``, ``DELETE``的HTTP方法.
9. **Deny-override**: allow和deny授权均支持，deny优先于allow.
10. **Priority**: 策略规则像防火墙规则一样拥有优先级.

## 工作原理

在 jCasbin 中, 一个访问控制模型被抽象在一个基于 **PERM 元模型 (Policy, Effect, Request, Matchers)**的CONF文件中. 所以切换或者更新项目的授权机制只需要简单的更改一个配置. 用户可以通过组合现有的模型来自定义访问控制模型. 例如，用户可以将RBAC的角色和ABAC的属性组合到一个模型，共享同一套策略规则.

jCasbin 中最基本最简单的模型是ACL. ACL模型配置如下:

```ini
# Request definition
[request_definition]
r = sub, obj, act

# Policy definition
[policy_definition]
p = sub, obj, act

# Policy effect
[policy_effect]
e = some(where (p.eft == allow))

# Matchers
[matchers]
m = r.sub == p.sub && r.obj == p.obj && r.act == p.act
```

一个ACL模式的例子如下:

```
p, alice, data1, read
p, bob, data2, write
```

表示:

- alice 有权限 read data1
- bob 有权限 write data2

## 特性

jCasbin的功能:

1. 以传统的 ``{subject, object, action}`` 格式或一种自定义的格式执行策略, 均支持allow和deny授权.
2. 处理访问控制模型的存储和他的策略.
3. 管理角色-用户和角色-角色（即RBAC的等级角色）的映射关系.
4. 支持内置的超级用户 ``root`` 或 ``administrator``. 一个超级用户可以做任何事而不需要额外的权限.
5. 多种内置的操作符来支持规则匹配. 例如, ``keyMatch`` 可以模式 ``/foo*``可以被映射到一个资源 ``/foo/bar``.

jCasbin没有的功能:

1. 认证 (即登录时 ``username`` 和 ``password`` 的验证)
2. 管理用户和角色列表. 我们相信项目本身来管理这些元素更为便利. 用户通常有密码, jCasbin不被设计为一个密码容器. 然而, jCasbin保存RBAC场景下的 用户-角色 映射关系. 

## 安装

Maven:

```
<dependency>
  <groupId>org.casbin</groupId>
  <artifactId>jcasbin</artifactId>
  <version>1.1.0</version>
</dependency>
```

## 文档

https://casbin.org/docs/en/overview

## 在线编辑器

用户可以在浏览器中使用在线编辑器 (http://casbin.org/editor/) 来编写 jCasbin 模型和策略. 它提供了如 ``syntax highlighting`` 和 ``code completion``的功能, 就像一个编程语言的IDE.

## 教程

https://casbin.org/docs/en/tutorials

## Get started

1. 使用一个模型文件和一个策略文件创建一个jCasbin执行器:

    ```java
    Enforcer enforcer = new Enforcer("path/to/model.conf", "path/to/policy.csv");
    ```

注意: 你还可以一个使用数据库而不是文件存储的策略来初始化执行器, 参照 [策略持久化](#策略持久化) 小节.

2. 在鉴权返回时在代码中增加一个执行钩子:

    ```java
    String sub = "alice"; // the user that wants to access a resource.
    String obj = "data1"; // the resource that is going to be accessed.
    String act = "read"; // the operation that the user performs on the resource.

    if (enforcer.enforce(sub, obj, act) == true) {
        // permit alice to read data1
    } else {
        // deny the request, show an error
    }
    ```

3. 除了静态的策略文件之外, jCasbin还提供了在执行时权限管理的API. 例如, 你可以获取分配给一个用户的所有角色，如下:

    ```java
    Roles roles = enforcer.getRoles("alice");
    ```

参照[策略管理API](#策略管理).

4. Please refer to the [src/test](https://github.com/casbin/jcasbin/tree/master/src/test) package for more usage.

## 策略管理

jCasbin提供了两组API来管理权限:

- [Management API](https://github.com/casbin/jcasbin/blob/master/src/main/java/org/casbin/jcasbin/main/ManagementEnforcer.java): the primitive API that provides full support for jCasbin policy management. See [here](https://github.com/casbin/jcasbin/blob/master/src/test/java/org/casbin/jcasbin/main/ManagementAPIUnitTest.java) for examples.
- [RBAC API](https://github.com/casbin/jcasbin/blob/master/src/main/java/org/casbin/jcasbin/main/Enforcer.java): a more friendly API for RBAC. This API is a subset of Management API. The RBAC users could use this API to simplify the code. See [here](https://github.com/casbin/jcasbin/blob/master/src/test/java/org/casbin/jcasbin/main/RbacAPIUnitTest.java) for examples.

我们还提供了一个web UI来管理模型和策略:

![model editor](https://hsluoyz.github.io/casbin/ui_model_editor.png)

![policy editor](https://hsluoyz.github.io/casbin/ui_policy_editor.png)

## 策略持久化

在jCasbin中, 策略的存储有一个适配器提供 (即一个jCasbin的中间件). 为了轻量级, 我们没有把适配器的代码放到核心库中 (除了默认的文件适配器). 一个完整的适配器列表如下. 欢迎任何三方贡献一个新的适配器, 请通知我们，我将这个适配器放到下面的列表中:

Adapter | 类型 | 作者 | 描述
----|------|----|----
[File Adapter (built-in)](https://github.com/casbin/casbin/wiki/Policy-persistence#file-adapter) | File | jCasbin | Persistence for [.CSV (Comma-Separated Values)](https://en.wikipedia.org/wiki/Comma-separated_values) files
[JDBC Adapter](https://github.com/jcasbin/jdbc-adapter) | JDBC | jCasbin | MySQL, Oracle, PostgreSQL, DB2, Sybase, SQL Server are supported by [JDBC](https://docs.oracle.com/cd/E19226-01/820-7688/gawms/index.html)

适配器详情请参照文档: https://github.com/casbin/casbin/wiki/Policy-persistence

## 角色管理

角色管理器是用来管理jCasbin中的 RBAC 角色层级 (用户-角色映射). 一个角色管理器可以从jCasbin策略规则或外部源如：LDAP, Okta, Auth0, Azure AD等来获取角色数据. 我们支持不同的角色管理的实现. 为了保证轻量级, 我们没有防止角色管理器的代码在核心库(除了默认的角色管理器). 一个完整的jCasbin角色管理器列表如下. 欢迎任何三方贡献一个新的角色管理器器, 请通知我们，我将这个适配器放到下面的列表中:

Role manager | 作者 | 描述
----|----|----
[Default Role Manager (built-in)](https://github.com/casbin/jcasbin/blob/master/src/main/java/org/casbin/jcasbin/rbac/DefaultRoleManager.java) | jCasbin | Supports role hierarchy stored in jCasbin policy

对于开发者:所有的角色管理必须实现 [RoleManager](https://github.com/casbin/jcasbin/blob/master/src/main/java/org/casbin/jcasbin/rbac/RoleManager.java) 接口. [Default Role Manager](https://github.com/casbin/jcasbin/blob/master/src/main/java/org/casbin/jcasbin/rbac/DefaultRoleManager.java) 可以用来作为一个参考实现.

## 样例

Model | 模型文件 | 策略文件
----|------|----
ACL | [basic_model.conf](https://github.com/casbin/jcasbin/blob/master/examples/basic_model.conf) | [basic_policy.csv](https://github.com/casbin/jcasbin/blob/master/examples/basic_policy.csv)
ACL with superuser | [basic_model_with_root.conf](https://github.com/casbin/jcasbin/blob/master/examples/basic_with_root_model.conf) | [basic_policy.csv](https://github.com/casbin/jcasbin/blob/master/examples/basic_policy.csv)
ACL without users | [basic_model_without_users.conf](https://github.com/casbin/jcasbin/blob/master/examples/basic_without_users_model.conf) | [basic_policy_without_users.csv](https://github.com/casbin/jcasbin/blob/master/examples/basic_without_users_policy.csv)
ACL without resources | [basic_model_without_resources.conf](https://github.com/casbin/jcasbin/blob/master/examples/basic_without_resources_model.conf) | [basic_policy_without_resources.csv](https://github.com/casbin/jcasbin/blob/master/examples/basic_without_resources_policy.csv)
RBAC | [rbac_model.conf](https://github.com/casbin/jcasbin/blob/master/examples/rbac_model.conf)  | [rbac_policy.csv](https://github.com/casbin/jcasbin/blob/master/examples/rbac_policy.csv)
RBAC with resource roles | [rbac_model_with_resource_roles.conf](https://github.com/casbin/jcasbin/blob/master/examples/rbac_with_resource_roles_model.conf)  | [rbac_policy_with_resource_roles.csv](https://github.com/casbin/jcasbin/blob/master/examples/rbac_with_resource_roles_policy.csv)
RBAC with domains/tenants | [rbac_model_with_domains.conf](https://github.com/casbin/jcasbin/blob/master/examples/rbac_with_domains_model.conf)  | [rbac_policy_with_domains.csv](https://github.com/casbin/jcasbin/blob/master/examples/rbac_with_domains_policy.csv)
ABAC | [abac_model.conf](https://github.com/casbin/jcasbin/blob/master/examples/abac_model.conf)  | N/A
RESTful | [keymatch_model.conf](https://github.com/casbin/jcasbin/blob/master/examples/keymatch_model.conf)  | [keymatch_policy.csv](https://github.com/casbin/jcasbin/blob/master/examples/keymatch_policy.csv)
Deny-override | [rbac_model_with_deny.conf](https://github.com/casbin/jcasbin/blob/master/examples/rbac_with_deny_model.conf)  | [rbac_policy_with_deny.csv](https://github.com/casbin/jcasbin/blob/master/examples/rbac_with_deny_policy.csv)
Priority | [priority_model.conf](https://github.com/casbin/jcasbin/blob/master/examples/priority_model.conf)  | [priority_policy.csv](https://github.com/casbin/jcasbin/blob/master/examples/priority_policy.csv)

## 采纳者

### Web frameworks

- [Spring Boot](https://projects.spring.io/spring-boot/): 更简单的创建spring功能的应用与服务, 通过插件: [jcasbin-springboot-plugin](https://github.com/jcasbin/jcasbin-springboot-plugin)
- [Vert.x](https://vertx.io/): 一个构建JVM交互式应用的工具集，通过插件: [vertx-auth-jcasbin](https://github.com/vert-x3/vertx-auth/pull/215)
- [JFinal](http://www.jfinal.com/): 一个简单，轻量，快速，无依赖，可扩展的Java WEB + ORM框架，通过插件: [jcasbin-jfinal-plugin](https://github.com/jcasbin/jcasbin-jfinal-plugin)
- [Nutz](https://nutzam.com/): 所有java开发者的Web框架(MVC/IOC/AOP/DAO/JSON)，通过插件: [jcasbin-nutz-plugin](https://github.com/jcasbin/jcasbin-nutz-plugin)
- [mangoo I/O](https://github.com/svenkubiak/mangooio): 一个直观的, 轻量, 高性能的全栈 Java web 框架，通过内置的插件: [AuthorizationService.java](https://github.com/svenkubiak/mangooio/blob/e8c647a3f7c427bce27377025bec074f6d767f50/mangooio-core/src/main/java/io/mangoo/services/AuthorizationService.java)

## License

版权证书 [Apache 2.0 license](LICENSE).

## Contact

如果有问题或功能需求, 请联系我们. 欢迎PR.
- https://github.com/casbin/jcasbin/issues
- hsluoyz@gmail.com
- Tencent QQ group: [546057381](//shang.qq.com/wpa/qunwpa?idkey=8ac8b91fc97ace3d383d0035f7aa06f7d670fd8e8d4837347354a31c18fac885)
