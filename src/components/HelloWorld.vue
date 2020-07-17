<template>
  <div class="hello">
    <button @click="handleTest">测试</button>
    <button @click="handleTest2">虎符</button>
    <button @click="handleTest3">后端</button>
    <button @click="getHeaders">后端2</button>
  </div>
</template>

<script>
import { Signer, Context } from 'jdcloud-sdk-signer'
export default {
  name: 'HelloWorld',
  methods: {
    handleTest() {
      let ctx=new Context('192.168.180.18','/v1/regions/cn-north-1/buckets','GET',null,'oss')  //可直接讲req.headers 传入
      ctx.regionId='cn-north-1'
      ctx.query=ctx.buildQuery({a:1,c:2,b:3})
      ctx.headers.set('content-type','application/json')
      ctx.buildNonce()

      let credentials= {
          accessKeyId : 'accessKeyId',
          secretAccessKey: 'secretAccessKey'
      }

      let signer=new Signer(ctx,credentials)
      signer.setSignableHeaders([
        'content-type',
        'host',
        'x-jdcloud-date',
        'x-jdcloud-nonce'
      ])
      let auth= signer.sign(new Date())
      ctx.headers.set('Authorization',auth)
      console.log("GET签名为：",auth)

      console.log('------------------------')

      ctx.query=null
      ctx.body=JSON.stringify({a:1,b:2})
      ctx.method='POST'
      signer=new Signer(ctx,credentials)
      auth= signer.sign(new Date())
      console.log("POST签名为：",auth)
    },

    handleTest2() {
      let ctx=new Context(
        'hufu.cn-north-1.jdcloud-api.net',
        '/order/getSensitiveData',
        'POST',
        null,
        'oss' // oss
      )  //可直接讲req.headers 传入
      ctx.regionId='cn-north-1'
      ctx.query=ctx.buildQuery({
        app_key: 'B62D1748F2C84E6D64A5C654FAE2A2A8',
        customerId: '600001',
        method: 'jingdong.hufu.order.getSensitiveData',
        // sign_method: 'md5',
      })
      ctx.headers.set('content-type','application/json')
      ctx.buildNonce()
  
      let credentials= {
          accessKeyId : 'B62D1748F2C84E6D64A5C654FAE2A2A8',
          secretAccessKey: 'B986ED5AE69AFC327888A45C6D898762'
      }
  
      let signer=new Signer(ctx,credentials)
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

      const bodyData = {
        "orders_nos":"106174775642,106170167188",
        "token":"562cabe010e8451da395e61e927919592fiz"
      }
   
      ctx.body=JSON.stringify(bodyData)
      // signer=new Signer(ctx,credentials)
      let auth= signer.sign(new Date())
      ctx.headers.set('Authorization',auth)
  
      // console.log("POST签名为：",auth)
      console.log(signer)
  
      // const sign = signer.headers.get('Authorization').split('Signature=')[1]
      window.fetch(
        // 'https://hufu.cn-north-1.jdcloud-api.net/hufu/order/getSensitiveData?app_key=B62D1748F2C84E6D64A5C654FAE2A2A8',
  
        'http://hufu.cn-north-1.jdcloud-api.net/order/getSensitiveData?method=jingdong.hufu.order.getSensitiveData&app_key=B62D1748F2C84E6D64A5C654FAE2A2A8&customerId=600001',
        {
          method: 'post',
          headers: {
            'Authorization': signer.headers.get('Authorization'),
            'content-type': signer.headers.get('content-type'),
            'x-jdcloud-nonce': signer.headers.get('x-jdcloud-nonce'),
            'x-jdcloud-date': signer.headers.get('x-jdcloud-date'),
            'host': signer.headers.get('host'),
          },
          body: JSON.stringify(bodyData)
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

    handleTest3() {
      // url = "https://hufu.cn-north-1.jdcloud-api.net/order/getSensitiveData?app_key=B62D1748F2C84E6D64A5C654FAE2A2A8&customerId=600001&method=jingdong.hufu.order.getSensitiveData";
      var headers = {
          "Content-Type": "application/json",
          // "Host": "hufu.cn-north-1.jdcloud-api.net",
          "X-Jdcloud-Nonce": "b0f6893b-4e5c-457d-83e3-95d20feaebeb",
          "X-Jdcloud-Date": "20200716T183225Z",
          "Authorization": "JDCLOUD2-HMAC-SHA256 Credential=B62D1748F2C84E6D64A5C654FAE2A2A8/20200717/cn-north-1/wn8l9g38jznp/jdcloud2_request, SignedHeaders=content-type;host;x-jdcloud-date;x-jdcloud-date;x-jdcloud-nonce, Signature=b4a5d94bd8feec8b3ee3af8c6ddc9eb36faf725b5a00741920c94520e4d3d307"
      };

      fetch(
        'https://hufu.cn-north-1.jdcloud-api.net?method=jingdong.hufu.order.getSensitiveData&app_key=B62D1748F2C84E6D64A5C654FAE2A2A8&customerId=600001',
        {
          method: 'post',
          body: JSON.stringify({
            "orders_nos":"106174775642,106170167188",
            "token":"562cabe010e8451da395e61e927919592fiz"
          }),
          headers
        }
      )

      // $.ajax({
      //     type: "POST",
      //     url: url,
      //     data:
      //         JSON.stringify({
      //             "orders_nos":"106174775642,106170167188",
      //             "token":"562cabe010e8451da395e61e927919592fiz"
      //         })
      //     ,
      //     beforeSend: function (xhr) {
      //         for (key in headers) {
      //             if (headers.hasOwnProperty(key)) {
      //                 xhr.setRequestHeader(key, headers[key])
      //             }
      //         }
      //     },
      //     success: function (msg) {
      //         if (callbackFunction)
      //         callbackFunction(msg);
      //     },
      //     error: function (xhr, textStatus, errorThrown) {
      //         alert(xhr.responseText);
      //     }
      //   });
      // }
    },

    getHeaders() {
      fetch(
        'http://192.168.7.94:30003/sign?method=jingdong.hufu.order.getSensitiveData&app_key=B62D1748F2C84E6D64A5C654FAE2A2A8&customerId=600001',
        {
          method: 'post',
          headers: {
            'Content-Type': 'application/json',
            'X-Jdcloud-Nonce': 'b0f6893b-4e5c-457d-83e3-95d20feaebeb',
            'X-Jdcloud-Date': '20200716T183225Z',
            'Authorization': 'Bearer 9f5ae678-7181-46ea-b60b-6939a88ac040'
          },
          body: JSON.stringify({"orders_nos":"106174775642,106170167188","token":"562cabe010e8451da395e61e927919592fiz"})
        }
      )
    }
  },
}
</script>

<style scoped>

</style>
