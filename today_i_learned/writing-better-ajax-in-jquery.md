참고 링크
https://medium.com/coding-design/writing-better-ajax-8ee4a7fb95f<br>

```
$.ajax({
    data: someData,
    dataType: 'json',
    url: '/path/to/script',
    success: function(data, textStatus, jqXHR) {
        // When AJAX call is successfuly
        console.log('AJAX call successful.');
        console.log(data);
    },
    error: function(jqXHR, textStatus, errorThrown) {
        // When AJAX call has failed
        console.log('AJAX call failed.');
        console.log(textStatus + ': ' + errorThrown);
    },
    complete: function() {
        // When AJAX call is complete, will fire upon success or when error is thrown
        console.log('AJAX call completed');
    };
});
```
jQuery.ajax() api를 통해 ajax 호출

하지만 1. ajax response를 통해 받아야 할 데이터에 의존적인 함수나 이벤트들은 success 핸들러 안에 작성돼야 하기 때문에,
data에 접근하기 위한 반복적인 코드가 많이 발생하고 depth가 깊어져 가독성을 해친다.
2. 체이닝과 조합이 어렵다. 비동기적으로 발생하는 각각의 요청에 대한 응답이 언제 올지 예측하기 어려우므로.
3. callback api들의 deprecation 문제 

다행히 .ajax()는 Promise interface를 구현한 api도 함께 제공한다.  

...업데이트중...
