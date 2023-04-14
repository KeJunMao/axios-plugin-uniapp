<img src="./assets/logo.svg" alt="logo of @uni-helper/axios-adapter repository" width="100" height="100" align="right" />

# @uni-helper/axios-adapter

> 适用于 Vue2 和 Vue3 的 uniapp Axios 适配器

## 安装

```
pnpm i @uni-helper/axios-adapter axios
```

## 使用

### 基本用法

```ts
import axios from "axios";
import { createUniAppAxiosAdapter } from "@uni-helper/axios-adapter";

const instance = axios.create({
  adapter: createUniAppAxiosAdapter(),
});
```

### 与 useAxios 一起使用

```ts
import axios from "axios";
import { useAxios } from "@vueuse/integrations/useAxios";
import { createUniAppAxiosAdapter } from "@uni-helper/axios-adapter";

const instance = axios.create({
  adapter: createUniAppAxiosAdapter(),
});

const { data, isFinished } = useAxios("/posts", instance);
```

### 上传和下载

```ts
import axios from "axios";
import { createUniAppAxiosAdapter } from "@uni-helper/axios-adapter";

const instance = axios.create({
  adapter: createUniAppAxiosAdapter(),
});

// 一个假文件
const data = new File([new Blob()], "emptyFile");

// 下载
instance.download("/");
// or
instance.request({
  url: "/",
  method: "download",
});

// 上传
instance.upload("/", data);
// or
instance.request({
  url: "/",
  method: "upload",
  data,
});
```

### 小程序
小程序没有 FormData 和 Blob 对象, 因此你需要额外的库来兼容，并通过自定义一个 vite 插件来兼容

```bash
pnpm add miniprogram-formdata miniprogram-blob
```

```ts
// vite.config.ts
export default defineConfig({
  plugins: [
    {
      transform(code, id) {
        if (process.env.UNI_PLATFORM?.includes("mp")) {
          if (id.includes("/form-data/lib/browser.js")) {
            return {
              code: code.replace("window", "globalThis"),
            };
          }
          if (id.includes("/axios/lib/platform/browser/classes/FormData.js")) {
            return {
              code: `import FormData from 'miniprogram-formdata';\nexport default FormData;`,
            };
          }
          if (id.includes("/axios/lib/platform/browser/index.js")) {
            return {
              code: `import Blob from 'miniprogram-blob'\n${code}`,
            };
          }
        }
      },
    },
  ]
})
```

## 客户端类型

提供了 upload 和 download 方法的类型提示，及 AxiosRequestConfig 支持传递 uniapp 特有参数

```ts
/// <reference types="@uni-helper/axios-adapter/client" />
```
