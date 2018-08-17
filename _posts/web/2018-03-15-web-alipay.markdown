---
layout: post
category: "web"
title:  "支付宝当面付JSAPI"
tags: [Web]
---


> 调用统一收单创建接口，整体流程是在支付宝打开调用页面后点击下单，商户后台调用统一收单创建接口，调用成功返回结果给下单页面，下单页面根据tradeNo的值执行jJSAPI吊起支付宝钱包然后付款，付款成功后商户后台的异步通知地址收到支付结果通知

[支付文档](https://docs.open.alipay.com/api_1/alipay.trade.create)



> [快速接入](https://docs.open.alipay.com/194/105170/)  


1. 在支付宝开放平台创建了应用APP
1. 配置商户公钥，商户私钥自己保存
1. 确定签名方式，建议RSA2
1. 配置授权域名，只有授权的域名才能拿到auth_code
2. 配置支付宝网关
2. 签约当面付

<!-- more -->


> 引入依赖  

	<dependency>
	  <groupId>com.alipay.sdk</groupId>
	  <artifactId>alipay-sdk-java</artifactId>
	  <version>3.3.4.ALL</version>
	</dependency>



> 配置AlipayClient

	public class AliConfig {
	    public static final String app_id = "创建的APPID";
	    public static final String your_private_key = "商户私钥";
	    public static final String alipay_public_key = "支付宝公钥";
	    //编码格式
	    public static final String CHARSET = "utf-8";
	    public static final String format = "json";
	    //签名方式
	    public static final String sign_type = "RSA2";
	    //请求网关地址，当前配置的是沙箱环境的网关，正式环境网关填写https://openapi.alipay.com/gateway.do
	    public static final String url="https://openapi.alipaydev.com/gateway.do";
	    //第三方授权的token值
	    public static final String  APP_AUTH_TOKEN ="201805BBfbd5b3be0c554f98b7bbe641ed676E68";
	    
	    public static AlipayClient alipayclient() {
	        AlipayClient alipayClient = new DefaultAlipayClient(url, app_id,your_private_key, format, CHARSET, alipay_public_key, sign_type);
	        return alipayClient;
	    }

	}
	    


> 配置发起支付工具类，请求成功返回支付宝流水号  

	public static String requestAliCreatePayApi(AlipayObject bizModel, String authToken) throws AlipayApiException {
	        AlipayTradeCreateRequest request = new AlipayTradeCreateRequest();
	        request.setNotifyUrl("回调地址");
	        request.setBizModel(bizModel)；
	        if(authToken != null){//第三方授权token，用于isv服务商代发起支付
	            request.putOtherTextParam("app_auth_token", authToken);
	        }
	        AlipayTradeCreateResponse response;
	        response = AliConfig.alipayclient().execute(request);
	        System.out.println("支付宝流水号：" + response.getTradeNo());
	        return response.getTradeNo();
	    }



> 后台静态授权，重定向到支付宝，获取auth_code,沙箱环境为alipaydev，线上环境为alipay  
	
	response.sendRedirect("https://openauth.alipaydev.com/oauth2/publicAppAuthorize.htm?app_id=" +
	 AliConfig.app_id+ "&redirect_uri=http://qif.tunnel.qydev.com/parking/result.do&scope=auth_base");
	                
          


> 根据auth_code获取支付宝用户ID,前提是支付宝访问过来的才能拿到auth_code，而且只有在创建的APP中配置授权域名，支付宝才能访问该域名下的地址
	
	public static String getAliPayUserId(String authCode) throws AlipayApiException {
	        AlipaySystemOauthTokenRequest request = new AlipaySystemOauthTokenRequest();
	        request.setCode(authCode);
	        request.setGrantType("authorization_code");
	        AlipaySystemOauthTokenResponse oauthTokenResponse = AliConfig.alipayclient().execute(request);
	        return oauthTokenResponse.getUserId();
	    }




> 前端页面异步请求发起支付  
	
	$("#toPay").click(function () {
        $.ajax({
            type:'post',
            url:"/parking/toPay.do",
            data:{userId:$("#userId").val()},
            success:function(data){
                AlipayJSBridge.call("tradePay",{
                    "tradeNO": data
                }, function(result){
                    if(result.resultCode == '9000'){
                        window.location.href = "/result.jsp";
                    }else{
                        alert(result.resultCode);
                    }
                });
            },
            error : function() {
                alert("请求异常！");
            }
        });
    });




> 后台封装业务，发起支付  

	@RequestMapping("/toPay.do")
    @ResponseBody
    public String toPay(HttpServletResponse response,String userId) throws IOException, AlipayApiException {
        String authToken = null;
        AlipayTradeCreateModel payInfo = new AlipayTradeCreateModel();
        payInfo.setBody("沙箱测试");
        payInfo.setSubject("沙箱测试");
        payInfo.setTimeoutExpress("5m");
        payInfo.setTotalAmount("50");
        payInfo.setBuyerId(userId);
        payInfo.setOutTradeNo("20180205000001");
        String orderNo = AliUtil.requestAliCreatePayApi(payInfo,authToken);
        return orderNo;
    }