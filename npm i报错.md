`npm install xxxx --legacy-peer-deps`命令是什么？为什么可以解决下载时候产生的依赖冲突呢？
`npm install xxxx --legacy-peer-deps`命令与其说是告诉npm要去干什么，不如说是告诉npm不要去干什么。

`legacy`的意思：遗产/（软件或硬件）已过时但因使用范围广而难以替代的；而`npm install xxxx --legacy-peer-deps`命令用于绕过`peerDependency`里依赖的自动安装；它告诉`npm`忽略项目中引入的各个依赖模块之间依赖相同但版本不同的问题，以npm `v3-v6`的方式去继续执行安装操作。

所以其实该命令并没有真的解决冲突，而是忽略了冲突，以“过时”（v3-v6）的方式进行下载操作。