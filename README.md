iotdjs是访问iotd区块链的JavaScript开发包，它通过RPC API访问iotd节点， 同时包含了密钥签名、交易序列化等本地操作。

### 安装

使用npm安装nodejs包：

~$ npm install iotdjs@beta

如果要在浏览器里使用iotdjs，一种方法是本地构建：

~$ git clone https://github.com/IOTD-BY-VKT/iotdjs.git
~$ cd iotdjs  
~/iotdjs$ npm install  
~/iotdjs$ npm run build-web  

然后在~/iotdjs/build-web目录下就可以找到构建好的前端js文件了。

### 引入iotdjs包

在ES模块中使用import引入iotdjs包，例如：

import {Api,JsonRpc,RpcError} from 'iotdjs';  
import JsSignatureProviderfrom 'iotdjs/dist/iotdjs-jssig';// development only  

在nodejs的commonjs模块中，使用require引入iotdjs包，例如：  

const {Api,JsonRpc,RpcError}=require('iotdjs');  
const JsSignatureProvider=require('iotdjs/dist/iotdjs-jssig');    // development only  
const fetch =require('node-fetch');                             // node only; not needed in browsers  
const {TextEncoder,TextDecoder}=require('util');                 // node only; native TextEncoder/Decoder  
const {TextEncoder,TextDecoder}=require('text-encoding');        // React Native, IE11, and Edge Browsers only  


### 用法概述

#### 签名提供器

iotdjs中的签名提供器负责对交易进行签名。例如：

const defaultPrivateKey ="5JtUScZK2XEp3g9gh7F8bwtPTRAkASmNrrftmx4AxDKD5K4zDnr"; // useraaaaaaaa  

const signatureProvider =newJsSignatureProvider([defaultPrivateKey]);  

> 目前iotdjs中包含的JsSignatureProvider在内存中管理私钥，在浏览器里使用 这个签名提供器是不安全的，仅限开发环境使用。

#### JSON-RPC调用

JsonRpc类封装了iotd JSON-RPC调用，在Nodejs中使用时，记得设置fetch API：

const rpc = new JsonRpc('http://127.0.0.1:8888', { fetch });

#### API

在浏览器中使用

Api类时，需要声明textDecoder和textEncoder：

const api = newApi({ rpc, signatureProvider, textDecoder:newTextDecoder(), textEncoder:newTextEncoder()});

##### 交易提交

使用

Api实例的

transact()方法提交一个交易到区块链上，例如：

(async () => {  
    const result = await api.transact({  
    actions:[{  
      account:'eosio.token',  
      name:'transfer',  
      authorization:[{  
        actor:'useraaaaaaaa',  
        permission:'active',  
      }],  
      data:{from:'useraaaaaaaa',  
        to:'useraaaaaaab',  
        quantity:'0.0001 IOTD',  
        memo:'',  
      },  
    }]  
  }, {  
    blocksBehind:3,  
    expireSeconds:30,  
  });  
  console.dir(result);  
})();  

transact()的第二个参数是一个选项对象，可以包含以下字段：

- broadcast：是否广播交易，默认值：true
- blocksBehind：TAPOS字段，节点用来判断交易是否超时
- expiresSeconds：TAPOS字段，节点用来判断交易是否超时

##### 错误处理

使用

RpcError来处理RPC错误：

try{const result = await api.transact({...}catch(e){  
  console.log('\nCaught exception: '+ e);if(e instanceofRpcError)  
    console.log(JSON.stringify(e.json,null,2));  
}  

#### 运行测试用例

自动化单元测试：

~/iotdjs$ npm run test or yarn test  

web集成测试：

首先执行

npm run build-web ，然后打开

src/tests/web.html运行web集成测试。
