title: 基于HttpClientFactory的封装和使用
author: OdinSam
tags:
  - .Net Core
  - HttpClientFactory
categories:
  - .Net Core
abbrlink: 4ff6
date: 2021-06-11 06:08:00
---
> .Net Core2.0 版本以前 HttpClient 还是挺坑的，我们需要操心怎么建立如何释放，而且代码质量不好还会影响 HttpClient 的性能和访问速度。2.0版本以后 HttpClientFactory 的出现解决了我们所有的痛点，我们不须要关心如何建立 HttpClient 又如何释放它。经过它能够建立具备特定业务的HttpClient，并且能够很友好的和 DI 容器结合使用。

<!--more-->


#### 1. 首先在 Startup.cs 文件的 ConfigureServices 方法中注入 HttpClient
    
```csharp
var handler = new HttpClientHandler();
foreach (var cerItem in _Options.SslCers)
{
    if (!string.IsNullOrEmpty(cerItem.CerPath))
    {
        var clientCertificate = new X509Certificate2(cerItem.CerPath, cerItem.CerPassword);
        handler.ClientCertificates.Add(clientCertificate);
    }
}
var handlerWithCer = new HttpClientHandler();
foreach (var cerItem in _Options.SslCers)
{
    if (!string.IsNullOrEmpty(cerItem.CerPath))
    {
        var clientCertificate = new X509Certificate2(cerItem.CerPath, cerItem.CerPassword);
        handlerWithCer.ClientCertificates.Add(clientCertificate);
    }
}
services.AddHttpClient("OdinClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
services.AddHttpClient("OdinClientCer", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handlerWithCer);
```

这里我注入了两个 HttpClient，一个是没有证书的，一个是有证书的。<font color="red">如何在 Post 和 Get 的时候动态传递证书，有知道的小伙伴可以留言告诉我一下（我没找到这个解决的办法）。</font>

#### 2. 接下来可以在拦截器里开启 Request.Body 重复读取。
    
```csharp
context.HttpContext.Request.EnableBuffering();
```

#### 3. 封装获取Request.Body的方法
    
```csharp
public static class HttpRequestExtends
{
    public static string ReadRequestBody(this HttpRequest request)
    {
        var reader = new StreamReader(request.Body);
        var data = reader.ReadToEndAsync();
        request.Body.Seek(0, SeekOrigin.Begin);
        return data.Result;
    }
}
```

#### 4. 封装HttpClient方法(这里只是封装了 Get 和 Post 作为演示)

```csharp
public class OdinHttpClientFactory : IOdinHttpClientFactory
{
    public static async Task<T> GetRequestAsync<T>(string clientName, string uri, Dictionary<string, string> customHeaders = null, string mediaType = "application/json")
    {
        var clientFactory = OdinInjectHelper.GetService<IHttpClientFactory>();
        var client = clientFactory.CreateClient(clientName);
        var request = new HttpRequestMessage()
        {
            RequestUri = new Uri(uri),
            Method = HttpMethod.Get,
        };
        RequestHeaderAdd(request, customHeaders);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue(mediaType));
        return await GetResponseResult<T>(client, request);
    }

    public static async Task<T> PostRequestAsync<T>(string clientName, string uri, Object obj, Dictionary<string, string> customHeaders = null,
                                                string mediaType = "application/json", Encoding encoder = null)
    {
        var clientFactory = OdinInjectHelper.GetService<IHttpClientFactory>();
        var client = clientFactory.CreateClient(clientName);
        var request = new HttpRequestMessage()
        {
            RequestUri = new Uri(uri),
            Method = HttpMethod.Post,
        };
        RequestHeaderAdd(request, customHeaders);
        request.Content = GenerateContent(obj, mediaType, encoder);
        return await GetResponseResult<T>(client, request);
    }

    private static HttpContent GenerateContent(Object obj, string mediaType, Encoding encoder)
    {
        if (typeof(String) == obj.GetType())
        {
            return GenerateContent<String>(obj.ToString(), mediaType, encoder);
        }
        else
        {
            return GenerateContent<Object>(obj, mediaType, encoder);
        }
    }
    private static HttpContent GenerateContent<T>(T obj, string mediaType, Encoding encoder)
    {
        StringBuilder jsonContent = new StringBuilder();
        string sendContent = string.Empty;
        Dictionary<string, string> dic = ConvertPostDataToDictionary<T>(obj, encoder);
        if (mediaType == "application/json")
        {
            sendContent = JsonConvert.SerializeObject(dic);
        }
        else
        {
            sendContent = ConvertDictionaryToPostFormData(dic).ToString();
        }
        return new StringContent(
                        sendContent,
                        encoder == null ? Encoding.UTF8 : encoder,
                        mediaType);
    }

    private async static Task<T> PostResponseResult<T>(HttpClient client, HttpRequestMessage request)
    {
        var response = await client.SendAsync(request);
        if (response.IsSuccessStatusCode)
        {
            return GetResult<T>(response);
        }
        else
            throw new Exception("请求出错");
    }
    private async static Task<T> GetResponseResult<T>(HttpClient client, HttpRequestMessage request)
    {
        var response = await client.SendAsync(request);
        if (response.IsSuccessStatusCode)
        {
            return GetResult<T>(response);
        }
        else
            throw new Exception("请求出错");
    }
    private static void RequestHeaderAdd(HttpRequestMessage request, Dictionary<string, string> customHeaders)
    {
        if (customHeaders != null)
        {
            foreach (KeyValuePair<string, string> customHeader in customHeaders)
            {
                request.Headers.Add(customHeader.Key, customHeader.Value);
            }
        }
    }
    private static T GetResult<T>(HttpResponseMessage httpResponseMessage)
    {
        // 确认响应成功，否则抛出异常
        // result.EnsureSuccessStatusCode();
        if (typeof(T) == typeof(byte[]))
        {
            return (T)Convert.ChangeType(httpResponseMessage.Content.ReadAsByteArrayAsync(), typeof(T));
        }
        else if (typeof(T) == typeof(Stream))
        {
            return (T)Convert.ChangeType(httpResponseMessage.Content.ReadAsStreamAsync().Result, typeof(T));
        }
        else
        {
            if (typeof(T) == typeof(string))
                return (T)Convert.ChangeType(httpResponseMessage.Content.ReadAsStringAsync().Result, typeof(T));
            return JsonConvert.DeserializeObject<T>(httpResponseMessage.Content.ReadAsStringAsync().Result);
        }
    }

    public static Dictionary<string, string> ConvertPostDataToDictionary<T>(T obj, Encoding encoder = null)
    {
        Dictionary<string, string> dic = new Dictionary<string, string>();
        if (typeof(T) == typeof(String))
        {
            foreach (var item in obj.ToString().Split('&'))
            {
                dic.Add(
                    item.Split('=')[0],
                    encoder == null || encoder == Encoding.UTF8 ?
                    item.Split('=')[1]
                    :
                    item.Split('=')[1].ConvertStringEncode(Encoding.UTF8, encoder)
                    );
            }
        }
        else
        {
            foreach (var item in obj.GetType().GetRuntimeProperties())
            {
                dic.Add(item.Name,
                        encoder == null || encoder == Encoding.UTF8 ?
                        item.GetValue(obj).ToString()
                        :
                        item.GetValue(obj).ToString().ConvertStringEncode(Encoding.UTF8, encoder)
                        );
            }
        }
        return dic;
    }
    private static StringBuilder ConvertDictionaryToPostFormData(Dictionary<string, string> dic)
    {
        StringBuilder builder = new StringBuilder();
        if (dic != null)
        {
            bool hasParam = false;
            foreach (KeyValuePair<string, string> kv in dic)
            {
                string name = kv.Key;
                string value = kv.Value;
                // 忽略参数名或参数值为空的参数
                if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(value))
                {
                    if (hasParam)
                    {
                        builder.Append("&");
                    }
                    builder.Append(name);
                    builder.Append("=");
                    builder.Append(value);
                    hasParam = true;
                }
            }
        }
        return builder;
    }
}
```

#### 5. 最后封装获取Request.Body内容后二次封装的方法，因为我会遇到 一个 Post 请求但是 Url 还带有参数的情况，所以这里封装的稍微复杂一些
    
```csharp
public static RequestParamsModel GetRequestParams(this Controller controller, string paramFormat = null)
{
    HttpContext context = controller.HttpContext;
    var request = context.Request;
    JObject jobj = new JObject();
    RequestParamsModel requestParams = new RequestParamsModel();
    if (!string.IsNullOrEmpty(context.Request.QueryString.ToString()))
    {
        string param = request.QueryString.ToString().Substring(1);
        requestParams.RequestQueryString = JsonConvert.DeserializeObject<JObject>(JsonConvert.SerializeObject(OdinHttpClientFactory.ConvertPostDataToDictionary<string>(param)));
    }
    if (request.ContentType != null && request.ContentType.StartsWith("application/json"))
    {
        string param = request.ReadRequestBody();
        requestParams.RequestFormData = JsonConvert.DeserializeObject<JObject>(param);
    }
    if (request.ContentType != null &&
            (request.ContentType.StartsWith("text/plain") || request.ContentType.StartsWith("application/javascript") ||
            request.ContentType.StartsWith("text/html") || request.ContentType.StartsWith("application/xml")))
    {
        string param = request.ReadRequestBody();
        requestParams.RequestFormDataString = param.Replace("\r", "").Replace("\n", "").Replace(" ", "");
    }
    if (request.ContentType != null && request.ContentType.StartsWith("application/x-www-form-urlencoded"))
    {
        string param = request.ReadRequestBody();
        requestParams.RequestFormData = JsonConvert.DeserializeObject<JObject>(JsonConvert.SerializeObject(OdinHttpClientFactory.ConvertPostDataToDictionary<string>(param)));
    }
    if (request.ContentType != null && request.ContentType.StartsWith("multipart/form-data"))
    {
        Dictionary<string, string> dic = new Dictionary<string, string>();
        foreach (var kv in request.Form)
        {
            dic.Add(kv.Key, kv.Value);
        }
        requestParams.RequestFormData = JsonConvert.DeserializeObject<JObject>(JsonConvert.SerializeObject(dic));
        List<Dictionary<string, MemoryStream>> files = new List<Dictionary<string, MemoryStream>>();
        long filesize = 0;
        foreach (var file in request.Form.Files)
        {
            filesize += file.Length;
            if (filesize > 1024 * 1024 * 4)
                throw new Exception("文件过大无法上传，请联系管理员申请使用大文件上传服务器");
            var fileBytes = new Byte[file.Length];
            MemoryStream fileStream = new MemoryStream(fileBytes);
            file.CopyTo(fileStream);
            files.Add(new Dictionary<string, MemoryStream>() { { file.Name, fileStream } });
        }
        requestParams.RequestUploadFile = files;
    }
    return requestParams;
}
```

	其中文件上传大小可以通过配置文件限制，现在及时我们遇到有Get请求，但是带着FormData文件的情况，我们也可以正常获取所有信息，其中信息内容格式如下：
    
```csharp
public class RequestParamsModel
{
    /// <summary>
    /// Url 地址栏参数信息 自动转化为 JObject
    /// </summary>
    /// <value></value>
    public JObject RequestQueryString { get; set; }
    /// <summary>
    /// 当 请求内容包含 application/text application/xml text/plain 和 application/javascript 是，获取内容一律视为 string，后期再自行处理
    /// </summary>
    /// <value></value>
    public String RequestFormDataString { get; set; }
    /// <summary>
    /// FormData 请求时所有的键值对，自动转化为 JObject
    /// </summary>
    /// <value></value>
    public JObject RequestFormData { get; set; }
    /// <summary>
    /// FormData 请求时附带的文件，key为文件名 value为文件的stream格式
    /// </summary>
    /// <value></value>
    public List<Dictionary<string, MemoryStream>> RequestUploadFile { get; set; }
}
```

#### 6. 其中，这个封装并没有处理Body包含 binary 格式和 GraphQL 格式，如有需要可以自行扩展。此时，当我们遇到
    
```csharp
var obj = OdinHttpClientFactory.PostRequestAsync<OdinActionResult>("OdinClient",
                            "http://127.0.0.1:20303/api/v1/LinkTrack/pfda?id=4&name=admin",
                            new { User = "odinsam" });
```

	这样的请求时，我们就会得到如下内容:(OdinActionResult是我自己定义的一个统一返回格式而已)

```csharp
RequestQueryString - null
{"id":"4","name":"admin"}
RequestFormDataString - null
RequestFormData
{"User":"odinsam"}
RequestUploadFile - null
```


 





















