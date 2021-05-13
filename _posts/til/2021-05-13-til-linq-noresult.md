---
layout: post
title:  "2021-05-13"
date:   2021-05-13
categories: TIL
tags: 
comments: false
---
## Enumeration yielded no results

1. Web API 응답 값이 "[]" 일 경우  
    - 응답값에 Contains() 를 통해서 데이터 유무를 판별
    ```c#
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
            if(result.Contains("key")){
                // TODO : 응답처리
            }else{
                // TODO : null 처리
            }
        }
    }
    ```