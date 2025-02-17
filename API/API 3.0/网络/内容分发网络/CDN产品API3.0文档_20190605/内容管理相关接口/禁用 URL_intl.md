﻿## 1. API Description

API request domain name: cdn.tencentcloudapi.com.

DisableCaches blocks accesses to the specified URL on CDN. After the URL is blocked, The 403 error will be returned, meaning that  accessing the page or resource you were trying to reach is absolutely forbidden for some reason. (This API is in beta.)

Default API request rate limit: 40 requests/sec.

## 2. Input Parameters

The following parameters are required for requesting this API, including action-specific parameters and common parameters. For more information about common parameters for all requests, see [Common Request Parameters](/document/api/228/30977).

| Parameter name | Required | Type | Description |
|---------|---------|---------|---------|
| Action | Yes | String | Common parameter, the name of this API: DisableCaches |
| Version | Yes | String | Common parameter, the version of API: 2018-06-06 |
| Region | No | String | Common parameter; optional for this API. |
| Urls.N | Yes | Array of String | List of URLs to be blocked <br/>You can submit up to 100 entries at a time and up to 3,000 entries per day |

## 3. Output Parameters

| Parameter name | Type | Description |
|---------|---------|---------|
| CacheOptResult | [CacheOptResult](/document/api/228/30987#CacheOptResult) | Action result <br/>Note: This field may return null, indicating that no valid values can be obtained. |
| RequestId | String | The ID of the request. Each request returns a unique ID. The RequestId is required to troubleshoot issues. |

## 4. Sample

### Sample 1. Blocking an URL

#### Input Sample Code

```
https://cdn.tencentcloudapi.com/?Action=DisableCaches
&Urls.0=http://example.com/path/to.jpg
&<Common request parameter>
```

#### Output Sample Code

```
{
  "Response": {
    "RequestId": "f13cf55b-69e6-4937-8856-bd8965beea8c",
    "CacheOptResult": {
      "SuccessUrls": [
        "http://example.com/path/to.jpg"
      ],
      "FailUrls": []
    }
  }
}
```


## 5. Developer Resources

### API Explorer

**This tool provides various capabilities such as online call, signature verification, SDK code generation, and quick API retrieval that significantly reduce the difficulty of using TencentCloud API.**

* [API 3.0 Explorer](https://console.cloud.tencent.com/api/explorer?Product=cdn&Version=2018-06-06&Action=DisableCaches)

### SDK

TencentCloud API 3.0 integrates software development toolkits (SDKs) that support various programming languages to make it easier for you to call the APIs.

* [Tencent Cloud SDK 3.0 for Python](https://github.com/TencentCloud/tencentcloud-sdk-python)
* [Tencent Cloud SDK 3.0 for Java](https://github.com/TencentCloud/tencentcloud-sdk-java)
* [Tencent Cloud SDK 3.0 for PHP](https://github.com/TencentCloud/tencentcloud-sdk-php)
* [Tencent Cloud SDK 3.0 for Go](https://github.com/TencentCloud/tencentcloud-sdk-go)
* [Tencent Cloud SDK 3.0 for NodeJS](https://github.com/TencentCloud/tencentcloud-sdk-nodejs)
* [Tencent Cloud SDK 3.0 for .NET](https://github.com/TencentCloud/tencentcloud-sdk-dotnet)

### TCCLI

* [Tencent Cloud CLI 3.0](https://cloud.tencent.com/document/product/440/6176)

## 6. Error Codes

The following error codes are API business logic-related. For other error codes, see [Common Error Codes](/document/api/228/15694#.E5.85.AC.E5.85.B1.E9.94.99.E8.AF.AF.E7.A0.81).

| Error Code | Description |
|---------|---------|
| InternalError.CdnDbError | Internal data error. Please submit a ticket for troubleshooting. |
| InternalError.CdnSystemError | System error. Please submit a ticket for troubleshooting. |
| InvalidParameter.CDNStatusInvalidDomain | Invalid domain name status |
| InvalidParameter.CdnHostInvalidParam | Invalid domain name format. Please check and try again. |
| InvalidParameter.CdnInterfaceError | Internal API error. Please submit a ticket for troubleshooting. |
| InvalidParameter.CdnParamError | Parameter error. Please see the sample parameters in the documentation. |
| InvalidParameter.CdnStatInvalidDate | Invalid date. Please see the sample date in the documentation. |
| InvalidParameter.CdnStatInvalidProjectId | Incorrect project ID. Please check and try again. |
| LimitExceeded.CdnHostOpTooOften | Too frequent operations on domain name. |
| ResourceNotFound.CdnHostNotExists | This domain name does not exist under the account. Please check and try again. |
| ResourceNotFound.CdnUserNotExists | The CDN service has not been activated. Please activate it first before using this API. |
| UnauthorizedOperation.CdnAccountUnauthorized | The sub-account is unauthorized to query full data. |
| UnauthorizedOperation.CdnCamUnauthorized | No CAM policy is configured for the sub-account. |
| UnauthorizedOperation.CdnUserAuthFail | Fail to authenticate the CDN user. |
| UnauthorizedOperation.CdnUserAuthWait | CDN user authentication is pending . |
| UnauthorizedOperation.CdnUserIsSuspended | The CDN service has been suspended. Please restart it and try again. |
| UnauthorizedOperation.CdnUserNoWhitelist | You are not on the whitelist, so using this function is prohibited. |
