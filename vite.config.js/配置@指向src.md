### 下载path

`npm i path -D `

### vite.config.js

```js
import { resolve } from "path";
const pathResolve = (dir) => resolve(__dirname, dir);

export default defineConfig({
  resolve: {
    alias: {
      "@": pathResolve("./src"), // 新增
    },
  },
})

```