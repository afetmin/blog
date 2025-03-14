---
title: 如果一个npm包不满足需求，如何修改其部分功能
date: 2024-09-18T06:21:04Z
lastmod: 2024-09-18T06:37:14Z
categories: js
---

### 一、使用 Fork

Fork 源代码，通过在 GitHub 上或其他托管平台上 Fork 第三方包的源代码库。对其源代码进行修改，修改完成后将修改后的包发布到 npm 上。如果你不希望它是公开的，那么你可以搭建一个 npm 的私有包。直接将项目中的包切换我们自己发布的包。
<!-- more -->

### 二、提交 PR

如果你认为你的修改对其他用户也有帮助，可以向原始包的维护者提交 Pull Request（PR）。如果 PR 被接受并合并，那么你就可以直接使用未来版本的官方包。

### 三、本地修改和补丁

这种方式可以避免直接修改 node\_modules 目录下的代码，也确保了项目的其他成员或在其他环境中部署时能够应用同样的修改。

1. 在本地对包进行修改：直接在项目的 node\_modules 目录下找到并修改对应的第三方包文件。
2. 创建补丁文件：一旦完成了必要的修改，你可以使用 git diff 或其他差异比较工具来生成一个补丁文件。

   ```bash
   git diff > patches/third-party-package.patch
   ```

3. 应用补丁：为了自动化地在每次安装依赖时应用这个补丁，你可以使用如 patch-package 这样的工具。patch-package 允许在 node\_modules 中的包上应用补丁，并且这些补丁可以和你的项目代码一起被版本控制。

安装 patch-pacakge ，然后，将应用补丁的步骤添加到 package.json 中的 scripts 字段：

```bash
"scripts": {
  "postinstall": "patch-package"
}
```

这样，每次运行 npm install 时，postinstall 脚本都会执行，自动应用保存在 patches/目录下的所有补丁。

#### 生成补丁

假设我们要要修改 axios 包，那么我们可以直接在项目的 node\_modules/axios 目录下对 axios 进行必要的修改。这些修改可以是任何东西，从简单的配置更改到函数逻辑的更新。

使用 patch-package 生成一个补丁文件。这个命令会比较你对 node\_modules 中 axios 的修改，并将这些修改保存为一个补丁文件。

```bash
npx patch-package axios
```

执行这个命令后，patch-package 会在项目的根目录下创建一个 patches 目录（如果还没有的话），并在里面生成一个名为 axios+ 版本号.patch 的文件，其中版本号是你项目中使用的 axios 的版本。

### 四、包装三方包

创建一个新的文件（如 third-party-wrapper.js），在这个文件中导入第三方包，并实现需要修改或扩展的功能。

在项目中的其他部分，你可以直接引入并使用这个封装模块，而不是直接使用第三方包。这样，你就可以利用修改后的功能，同时避免了对第三方包的直接修改。

这种方法的好处是，它提供了一个清晰的隔离层，使得第三方包的任何更新不会直接影响到你对功能的定制。同时，这也使得维护和升级第三方包变得更加容易，因为你只需要在封装层中做出相应的调整。

### 总结

以上 4 种方式，通常提交 PR 和使用 Fork 是最推荐的，因为它们可以避免维护自定义修改所带来的长期负担。但是由于业务的紧急性，我们也可以选择后两种方式。

‍
