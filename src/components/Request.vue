<template>
  <div class="hello">
    <div class="form">
      <div>
        <span class="label">完整请求地址</span>
        <textarea v-model="fullPath" name="" id="" cols="20" rows="5"></textarea>
      </div>
      <div><span class="label">host</span><input v-model="host" type="text"></div>
      <div><span class="label">相对地址</span><input v-model="path" type="text"></div>
      <div><span class="label">method</span><input v-model="method" type="text"></div>
      <div><span class="label">serviceName</span><input v-model="serviceName" type="text"></div>
      <div><span class="label">regionId</span><input v-model="regionId" type="text"></div>
      <div><span class="label">query</span><textarea v-model="query" name="" id="" cols="60" rows="10"></textarea></div>
      <div><span class="label">body</span><textarea v-model="body" name="" id="" cols="60" rows="10"></textarea></div>
      <div><span class="label">accessKeyId</span><input v-model="credentials.accessKeyId" type="text"></div>
      <div><span class="label">secretAccessKey</span><input v-model="credentials.secretAccessKey" type="text"></div>
    </div>
    <button @click="handleTest">发送请求</button>
  </div>
</template>

<script>
import { Signer, Context } from 'jdcloud-sdk-signer'
export default {
  name: 'HelloWorld',
  data() {
    const queryData = {
      app_key: 'B62D1748F2C84E6D64A5C654FAE2A2A8',
      customerId: '600001',
      method: 'jingdong.hufu.order.getSensitiveData',
      // sign_method: 'md5',
    }
    const bodyData = {
    "orders_nos":"106174775642,106170167188",
    "token":"562cabe010e8451da395e61e927919592fiz"
    }
    return {
      fullPath: 'http://hufu.cn-north-1.jdcloud-api.net/order/getSensitiveData?method=jingdong.hufu.order.getSensitiveData&app_key=B62D1748F2C84E6D64A5C654FAE2A2A8&customerId=600001',
      path: '/order/getSensitiveData',
      host: 'hufu.cn-north-1.jdcloud-api.net',
      method: 'POST',
      regionId: 'cn-north-1',
      serviceName: 'oss',
      body: JSON.stringify(bodyData),
      query: JSON.stringify(queryData),
      credentials: {
        accessKeyId : 'B62D1748F2C84E6D64A5C654FAE2A2A8',
        secretAccessKey: 'B986ED5AE69AFC327888A45C6D898762'
      },
      bodyData,
      queryData
    }
  },
  methods: {
    handleTest() {
      let ctx=new Context(
        this.host,
        this.path,
        this.method,
        null,
        this.serviceName // oss
      )  //可直接讲req.headers 传入
      ctx.regionId=this.regionId
      ctx.query=ctx.buildQuery(JSON.parse(this.query))
      ctx.headers.set('content-type','application/json')
      ctx.buildNonce()
  
      let signer=new Signer(ctx, this.credentials)
      signer.setSignableHeaders([
        'content-type',
        'host',
        'x-jdcloud-date',
        'x-jdcloud-nonce'
      ])
      // let auth = signer.sign(new Date())
      // ctx.headers.set('Authorization', auth)
      // console.log("GET签名为：",auth)
  
      // console.log('------------------------')

   
      // ctx.body=JSON.stringify(this.bodyData)
      ctx.body=this.body

      // signer=new Signer(ctx,credentials)
      let auth= signer.sign(new Date())
      ctx.headers.set('Authorization',auth)
  
      // console.log("POST签名为：",auth)
      console.log(signer)
  
      // const sign = signer.headers.get('Authorization').split('Signature=')[1]
      window.fetch(
        // 'https://hufu.cn-north-1.jdcloud-api.net/hufu/order/getSensitiveData?app_key=B62D1748F2C84E6D64A5C654FAE2A2A8',
  
        this.fullPath,
        {
          method: 'post',
          headers: {
            'Authorization': signer.headers.get('Authorization'),
            'content-type': signer.headers.get('content-type'),
            'x-jdcloud-nonce': signer.headers.get('x-jdcloud-nonce'),
            'x-jdcloud-date': signer.headers.get('x-jdcloud-date'),
            'host': signer.headers.get('host'),
          },
          // body: JSON.stringify(this.bodyData)
          body: this.body
        },
      )
  
      // secret := ctx.credValues.SecretKey
      // date := makeHmac([]byte("JDCLOUD2"+secret), []byte(ctx.formattedShortTime))
      // region := makeHmac(date, []byte(ctx.Region))
      // service := makeHmac(region, []byte(ctx.ServiceName))
      // credentials := makeHmac(service, []byte("jdcloud2_request"))
      // signature := makeHmac(credentials, []byte(ctx.stringToSign))
      // ctx.signature = hex.EncodeToString(signature)
    },
  },
}
</script>

<style scoped>
  input, textarea {
    width: 600px;
  }
  .form {
    display: flex;
    flex-direction: column;
  }
  .form > div {
    margin-bottom: 10px;
  }
  .form > div > * {
    vertical-align: top;
  }
  .form .label {
    display: inline-block;
    width: 100px;
  }
</style>
