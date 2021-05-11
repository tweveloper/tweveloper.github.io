---
layout: post
title:  "2021-05-11"
date:   2021-05-11
categories: TIL
tags: 
comments: false
---
## WebAPI Array Parameter
1. .NET Core Web API
2. Newtonsoft.json
3. RestSharp

```C#
public void RESTApi(string uri){
    JObject param = new JObject();
    param.Add("param1","value1");
    param.Add("param2","value2");
    param.Add("array", JToken.FromObject(new [] { 1, 2, 3 }));

    RestClient client;
    var request = new RestRequest(Method.POST);

    client = new RestClient($"{uri}");
    client.Timeout = -1;

    // 헤더설정 application/json
    request.AddHeader("Content-Type", "application/json");
    // 바디 설정 application/json
    request.AddParameter("application/json", param, ParameterType.RequestBody);

    // 요청
    IRestResponse response = client.Execute(request);
    // 성공
    if (response.IsSuccessful)
    {
        var result = response.Content; // 응답 메시지
    }
}

```