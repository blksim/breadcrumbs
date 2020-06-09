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

```
$.ajax({
    data: someData,
    dataType: 'json',
    url: '/path/to/script'
}).done(function(data) {
    // If successful
   console.log(data);
}).fail(function(jqXHR, textStatus, errorThrown) {
    // If fail
    console.log(textStatus + ': ' + errorThrown);
});
```
.ajax() 메소드 뒤에 네이티브 jQuery 메소드 체이닝 가능

**$.ajax() 메소드를 변수에 할당한 후, 변수에 promise 콜백 함수 체이닝**
```

var ajaxCall = $.ajax({
    context: $(element),
    data: someData,
    dataType: 'json',
    url: '/path/to/script'
});

ajaxCall.done(function(data) {
    console.log(data);
});
```
가독성이 향상됨
ajax 호출부와 promise api 호출부가 분리되어 그 사이에 코드를 추가하기에 용이.
즉, jQuery 1.5 이후부터 구현된 promise와 deferred object는 .on() 메소드에 의한 DOM에서의 이벤트 버블링을 대기 시 코드 실행의 동기성과 관련있다.

**다수의 ajax 호출**
promise와 함께 다수의 ajax를 조합할 수 있다.
$.when()을 추가함으로써 상태를 리스닝하고 있기만 하면 된다.

```
var a1 = $.ajax({...}),
    a2 = $.ajax({...});

$.when(a1, a2).done(function(r1, r2) {
    // Each returned resolve has the following structure:
    // [data, textStatus, jqXHR]
    // e.g. To access returned data, access the array at index 0
    console.log(r1[0]);
    console.log(r2[0]);
});
```

**ajax 요청의 의존 체인**
다수의 ajax 요청도 체이닝할 수 있다. 예를 들어, 두 번째 ajax 호출이 첫 번째 ajax 호출을 통해 반환된 데이터에 의존하는 경우다. 이럴 경우 $.then()은 새 promise를 리턴하며, $.done()이나 또 다른 $.then() 메소드로 전달될 수 있다.
```
var a1 = $.ajax({
             url: '/path/to/file',
             dataType: 'json'
         }),
    a2 = a1.then(function(data) {
             // .then() returns a new promise
             return $.ajax({
                 url: '/path/to/another/file',
                 dataType: 'json',
                 data: data.sessionID
             });
         });

a2.done(function(data) {
    console.log(data);
});
```

**ajax 요청 모듈화하기**
요청을 생성한 후 ajax 객체를 리턴하기만 하면 된다.
같은 파라미터를 사용하는 두 ajax 호출을 할 경우,
```
// Generic function to make an AJAX call
var fetchData = function(query, dataURL) {
    // Return the $.ajax promise
    return $.ajax({
        data: query,
        dataType: 'json',
        url: dataURL
    });
}

// Make AJAX calls 
// 1. Get customer order
// 2  Get customer ID
var getOrder = fetchData(
    {
        'hash': '2528ce2ed5ff3891c71a07448a3003e5',
        'email': 'john.doe@gmail.com'
    }, '/path/to/url/1'),
    getCustomerID = fetchData(
    {
        'email': 'john.doe@gmail.com'
    }, '/path/to/url/2');
    
// Use $.when to check if both AJAX calls are successful
$.when(getOrder, getCustomerID).then(function(order, customer) {
    console.log(order.data);
    console.log(customer.data);
});

```
깊은 nesting이나, 반복 코드가 필요 없게 된다.

**deferred object로 배열 핸들링하기**
일련의 연속적인 ajax 호출 후 모든 요청이 완료됐는지 확인하고 싶을 경우 두 가지 옵션이 있다.
1. 빈 배열 생성 후, $.each() 메소드로 ajax 호출을 반복한 후 반환된 promise 객체를 배열에 넣는다.

2. .map() 메소드를 이용해 promises를 포함한 객체를 생성하고, .get()으로 배열을 반환한다.
$.when.apply($, array)를 사용하면 모든 ajax call을 evaluate할 수 있다.
```

// Let's say we have a click handler and fires off a series of AJAX request
$selector.on('click', function() {
    // Construct empty array
    var deferreds = [];
    
    // Loop using .each
    $(this).find('div').each(function() {
        var ajax = $.ajax({
            url: $(this).data('ajax-url'),
            method: 'get'
        });
        
        // Push promise to 'deferreds' array
        deferreds.push(ajax);
    });
    
    // Use .apply onto array from deferreds
    $.when.apply($, deferreds).then(function() {
        // Things to do when all is done
    });
});
```
```
// Let's say we have a click handler and fires off a series of AJAX request
$selector.on('click', function() {
    // Map returned deferred objects
    var deferreds = $(this).find('div').map(function() {
        var ajax = $.ajax({
            url: $(this).data('ajax-url'),
            method: 'get'
        });
        
        return ajax;
    });
    
    // Use .apply onto array from deferreds
    // Remember to use .get()
    $.when.apply($, deferreds.get()).then(function() {
        // Things to do when all is done
    });
});
```
