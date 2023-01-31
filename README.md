<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

* [管理系统组成](#%E7%AE%A1%E7%90%86%E7%B3%BB%E7%BB%9F%E7%BB%84%E6%88%90)
* [登陆](#%E7%99%BB%E9%99%86)
* [权限](#%E6%9D%83%E9%99%90)
  + [R-BAC权限模型](#r-bac%E6%9D%83%E9%99%90%E6%A8%A1%E5%9E%8B)
* [代理](#%E4%BB%A3%E7%90%86)
* [开发](#%E5%BC%80%E5%8F%91)
* [框架](#%E6%A1%86%E6%9E%B6)
* [构建工具](#%E6%9E%84%E5%BB%BA%E5%B7%A5%E5%85%B7)
* [代码规范](#%E4%BB%A3%E7%A0%81%E8%A7%84%E8%8C%83)
  + [eslint+prettier 的使用，如果想直接看如何配置 `.eslintrc.js` 和vscode的 `settings.json` 。可直接前往章节配置示例查看](#eslintprettier-%E7%9A%84%E4%BD%BF%E7%94%A8%E5%A6%82%E6%9E%9C%E6%83%B3%E7%9B%B4%E6%8E%A5%E7%9C%8B%E5%A6%82%E4%BD%95%E9%85%8D%E7%BD%AE-eslintrcjs-%E5%92%8Cvscode%E7%9A%84-settingsjson-%E5%8F%AF%E7%9B%B4%E6%8E%A5%E5%89%8D%E5%BE%80%E7%AB%A0%E8%8A%82%E9%85%8D%E7%BD%AE%E7%A4%BA%E4%BE%8B%E6%9F%A5%E7%9C%8B)
    - [vscode如何针对vue2项目使用eslint？](#vscode%E5%A6%82%E4%BD%95%E9%92%88%E5%AF%B9vue2%E9%A1%B9%E7%9B%AE%E4%BD%BF%E7%94%A8eslint)
    - [为什么要用prettier？以及如何使用？](#%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81%E7%94%A8prettier%E4%BB%A5%E5%8F%8A%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8)
    - [eslint 和prettier同时使用造成的规则冲突及解决办法](#eslint-%E5%92%8Cprettier%E5%90%8C%E6%97%B6%E4%BD%BF%E7%94%A8%E9%80%A0%E6%88%90%E7%9A%84%E8%A7%84%E5%88%99%E5%86%B2%E7%AA%81%E5%8F%8A%E8%A7%A3%E5%86%B3%E5%8A%9E%E6%B3%95)
    - [`.eslintrc.js` 和vscode 中 `settings.json` 配置示例](#eslintrcjs-%E5%92%8Cvscode-%E4%B8%AD-settingsjson-%E9%85%8D%E7%BD%AE%E7%A4%BA%E4%BE%8B)
  + [git提交前eslint校验](#git%E6%8F%90%E4%BA%A4%E5%89%8Deslint%E6%A0%A1%E9%AA%8C)
* [测试](#%E6%B5%8B%E8%AF%95)
* [部署相关](#%E9%83%A8%E7%BD%B2%E7%9B%B8%E5%85%B3)
* [服务质量维护](#%E6%9C%8D%E5%8A%A1%E8%B4%A8%E9%87%8F%E7%BB%B4%E6%8A%A4)
* [性能监控](#%E6%80%A7%E8%83%BD%E7%9B%91%E6%8E%A7)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 管理系统组成

# 登陆

SS0重定向

# 权限

## R-BAC权限模型

在常规运营系统中，一个合理的权限系统不仅能避免数据误操作，也能避免数据的泄漏。权限上的垂直越权指用户a有接口 `/search` 的权限，但是因为管控遗漏使得该用户也拥有了 `/query` 接口的权限。访问了不该访问的接口也即是垂直越权。水平越权指的用户通过同一个接口查询到了他不应该查到的数据。我们常见的权限模型是R-BAC `(Role-Based Access Control)` 权限模型，顾明思议该模型是基于角色来完成权限分配。在该模型中，我们可以将菜单url，接口等定义为基本资源。然后将不同的资源结合在一起定义为权限。之后在定义不同的角色关联上对应的权限。最后再将具体的用户关联上对应的角色。举一个简单的例子。
* 资源：普通菜单，管理员专属菜单，普通接口，管理员专属接口。
* 权限： 普通菜单权限（绑定了资源中的`普通菜单`）；普通接口权限（绑定了资源中的`普通接口`）； 管理员菜单权限(绑定了资源中的`管理员专属菜单`)；管理员接口权限（绑定了资源中的`管理员专属接口`）
* 角色：通用人员 (关联了权限中的`普通菜单权限`和`普通接口权限`); 管理员（关联了权限中的`管理员菜单权限`和`管理员接口权`）

![图 5](images/23aee08cf0581cf52c06d039635770e27d23f67280cb36022d58d47b956a7a1c.png)

  

通过上面的图，整个权限系统分为三层，资源，权限和角色。真实的用户只用和角色关联。其实从上面的图中可以看出权限那一层只是对资源的聚合，那为什么还需要单独定义一个权限这层呢，目的还是为了方便操作。因为角色那层的变动是较为频繁的。比如时常需要新增角色，这是如果直接绑定资源的话操作起来就很麻烦，具体例子就是假如存在一个权限叫 `文章的点赞与评价` ，里面聚合了数十个接口。这样新增角色的时候直接把该权限和新角色绑定上，新角色就能一次性关联上所有 `文章的点赞与评价` 功能相关的资源。

该权限模型的使用缺点：由于是需要建立角色然后将真实的用户与之关联。随着管理人员的变动，因为后面的接手人无法容易得理解之前已经存在的每个角色的职责划分。所以后续的管理人员在权限变动时往往倾向于重新新建角色来控制权限，从而造成多个角色间很容易出现资源交叉。随着时间推移，角色会越变越多，权限关系越来越难以理解和管理。动辄几十上百的权限和角色让人十分头大

# 代理

rpc后台多服务，前端nginx代理

# 开发

开发工具配置

# 框架

# 构建工具相关

## webpack

### webpack-chain

### webpack-merge

一个用来合并webpack配置文件的npm包, 其运算过程有点类似下面的方法，如果是数组则两个数组执行concat，如果是对象则同名key互相替换。

```
function merge_(obj1, obj2) {
  if (typeof obj1 !== typeof obj2) return obj2;
  if (Array.isArray(obj1)) {
    return obj1.concat(obj2);
  } else if (typeof obj1 === "object") {
    for (let key in obj1) {
      const ans = { ...obj1, ...obj2 };
      if (key in obj2) {
        ans[key] = merge_(obj1[key], obj2[key]);
      }
      return ans;
    }
    return obj2;
  } else {
    return obj2;
  }
}
```

## 脚手架cli

# 代码规范

## eslint+prettier 的使用，如果想直接看如何配置 `.eslintrc.js` 和vscode的 `settings.json` 。可直接前往章节[配置示例](#eslintrcjs-%E5%92%8Cvscode-%E4%B8%AD-settingsjson-%E9%85%8D%E7%BD%AE%E7%A4%BA%E4%BE%8B)查看

eslint是用来检查代码中是否有没有使用的变量，使用了不符合规范的写法。可以通过一套规则检查代码不符合规范的部分来实现整个项目代码规范的统一。

### vscode如何针对vue2项目使用eslint？

1、首先需要在vscode中安装eslint插件。可以顺便打开vscode的配置文件settings.json，检查其中的 `eslint.enable` 是不是被不小心设置成了false，这个选项默认情况是true。

```
{
    "eslint.enable": false, // 关闭eslint检查，
 }

```

还有一点就是需要在vscode开启保存时自动检查并修复的功能，打开vscode的 `settings.json` 配置文件。然后配置以下配置

```
{
	"editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    }
}
```

（点击vscode插件列表的小齿轮也能进行设置）

![图 3](images/195090616198b80218624c4a01ee68b5edef21d0410906768753d7b6ff036f2a.png)

  

2、后在项目中安装eslint

```
npm i eslint -D
```

3、在项目的根目录中新建eslint的配置文件（也可以package.json中的 `eslintConfig` 字段下配置，效果和配置文件一样的）。配置文件的格式可以是 `.eslintrc.js` , `.eslintrc.json` , `.eslintrc.yaml` . 下面以在vue2项目使用 `.eslintrc.js` 举例

先安装下几个插件 `@babel/core` , `@babel/eslint-parser` , `eslint-config-prettier` , `eslint-plugin-vue` , 后面会说到具体作用。其中 `@babel/core` 是因为 `@babel/eslint-parser` 依赖了它

```
npm i @babel/eslint-parser eslint-plugin-vue  eslint-config-prettier -D
```

```js
//.eslintrc.js
//支持对vue2文件进行格式校验的最基础配置就类似下面
module.exports = {
    env: {
        browser: true,
        es6: true,
        node: true,
    },
    parserOptions: {
        parser: "@babel/eslint-parser",
        sourceType: "module",
    },
    extends: ["eslint:recommended", "plugin:vue/essential", "prettier"],
    rules: {
        camelcase: "warn",
    },
};
```

> env：eslint会对全局变量进行校验，通过env可以对全局变量进行定义

![图 1](images/6f47387b0fa60115ab05f56a2f09a1039c8a49c41fa1fa39daa0518e8c1377b7.png)

> parser : 定义用什么解析器来解析js文件。这里，如果不设置的话默认用的是eslint的 `Espree` 。这里选比较常用的babel的解析器。能更好的配合babel。注意vue项目中如果需要用其他解析器，一定要配置在 `parserOptions` 参数下。因为解析vue文件需要用到 `eslint-plugin-vue ` 中的 `vue-eslint-parser` 。如果更改eslint的parser的话就不能解析vue文件了，所以只能将自定义的解析器（类似 `@babel/eslint-parser` ）放在 `parserOptions` 作为一个解析器参数给传给 `vue-eslint-parser` 中去

> extends: 继承其他已经配置好的规则。 `eslint:recommended` 是eslint包装好的核心规则，只要继承它就能使用社区已经总结定义好的一些规则配置。这个一般是必须的， `plugin:vue/essential` 是来与于插件 `eslint-plugin-vue` 中的配置，其定义了对vue2文件的解析，和对vue2一些语法的规则。（如果项目用的是vue3，可以直接使用插件 `eslint-plugin-vue` 中携带的另一套配置 `plugin:vue/vue3-recommended` ）。 `prettier` 是来自于插件 `eslint-config-prettier` 中携带的配置，这个主要用于把eslint中的那部分和vscode中prettier插件格式化规则冲突的部分给关闭，关闭的都是些和格式化相关的非必要规则。注意关闭的是eslint中的规则，而不是vscode中prettier插件的规则

> rules : 主要用来描述具体的规则，例如能不能使用单引号，缩进用多少这类。具体可以去[eslint规则页面](https://eslint.bootcss.com/docs/rules/)查看，如需调整按照官网上的名称进行更改即可

### 为什么要用prettier？以及如何使用？

 prettier不仅能格式化js，也能格式化html，json，css等许多类型的文件。为了方便我们处理各种类型的文件装上prettier插件能很好的帮助我们进行格式化。也顺带对js文件进行格式化。

vscode中使用prettier对代码进行格式化只需要安装 `Prettier - Code formatter` 插件。这样我们在编辑文件时鼠标右键就能选择使用prettier进行格式化

![图 4](images/5ab485654786b593e3e7fe0732430447b461cca1762d6c180400149447d0d030.png)

  

如果想要保存文件时自动使用prettier进行格式化的话需要开启vscode的开启代码保存时进行格式化的设置。然后将默认的格式化方式设置为prettier。具体的 `settings.json` 中对应的配置如下。当然默认的格式化工具可以通过单机鼠标右键->使用... 格式化文档 选项进行更改

```json
{
	"editor.formatOnSave": true, // 开启保存文件自动格式化代码
	"editor.defaultFormatter": "esbenp.prettier-vscode", // 默认的代码格式化工具
}
```

### eslint 和prettier同时使用造成的规则冲突及解决办法

因为eslint在保存时能够修复代码，同时prettier在保存时也会对代码进行格式化。两者的格式化规则中存在小部分冲突的规则。解决的办法有两个，一个是使用 `eslint-config-prettier` 包，该包能够忽略掉eslint中那些和prettier冲突的规则。另一个是使用是使用 `eslint-plugin-prettier` 包，这个也能忽略掉冲突的规则，不过如果代码的格式不满足prettier的规则的话是通过eslint暴漏出来。这里我就用前者举例

```
npm i eslint-config-prettier -D
```

然后在 `.eslintrc.js` 配置文件中使用该包中携带的配置 `prettier`

```
//.eslintrc.js
{
	  extends: ["eslint:recommended", "plugin:vue/essential","prettier"]
}
```

注意， `eslint-config-prettier` 是用来关闭eslint中那些和prettier冲突的格式化规则的。这里存在一个问题， `.eslintrc.js` 中 `rules` 配置中配置的规则优先级是很高的，不能被 `eslint-config-prettier` 插件忽略掉，所以尽量别在rules配那种和 `prettier` 冲突的格式化规则。那些会冲突在实际配的时候能很明显提示的，不用刻意关注。冲突的时候把自己配的相关规则删了即可

### `.eslintrc.js` 和vscode 中 `settings.json` 配置示例

```
npm i @babel/eslint-parser eslint-plugin-vue @babel/core eslint-config-prettier eslint -D
```

```
//.eslintrc.js
module.exports = {
    env: {
        browser: true,
        es6: true,
        node: true,
    }, 
    parserOptions: {
        parser: "@babel/eslint-parser",
        sourceType: "module",
    },
    extends: ["eslint:recommended", "plugin:vue/essential", "prettier"],
    rules: {
        camelcase: "warn",
        
    },
};
```

注意别把vscode配置中 `"eslint.enable": false` 设置给自己设置成了false。保证eslint开启。这个选项默认是开启的

```
//settings.json
{
	"editor.formatOnSave": true, 
	"editor.defaultFormatter": "esbenp.prettier-vscode",
	"eslint.enable": true,
	"editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
  },
	
}
```

## git提交前eslint校验

# 测试

测试流程，分环境部署

# 部署相关

docker 

安装

```
curl -sSL https://get.daocloud.io/docker | sh
```

docker是一个常驻服务，首次安装或者重启机器都需要将docker给启动起来后才能针对docker进行其他动作

```
sudo systemctl start docker
```

查看docker是否成功启动

```
sodu systemctl is-active docker 
```

需要开启操作系统的防火墙端口访问，不然无妨访问系统的指定端口

```
#查看已经开通的端口
firewall-cmd --list-port
#查看防火墙状态
firewall-cmd --state
#开启防火墙
systemctl start firewall
#关闭
systemctl stop firewall
#重启
systemctl restart firewall
#开发单个端口，以80端口为例，--permanent是用来表示机器重启后仍旧生效
firewall-cmd --zone=public --add-port=80/tcp --permanent 
#关闭端口
firewall-cmd --zone=public --remove-port=80/tcp --permanent 

```

cli、shell脚本、云服务器

# 服务质量维护

负载均衡、限流熔断
进程守护工具 Daemontools

# 性能监控

性能监控，数据埋点，指标统计
