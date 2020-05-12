# iamport-ionic(DEPRECATED)
해당 프로젝트는 아이오닉 상위 버전과 호환 및 외부 라이브러리 의존도를 해결하기 위해 [iamport-cordova](https://github.com/iamport/iamport-cordova) 플러그인으로 대체되어 DEPRECATED 되었습니다. `iamport-cordova` 플러그인으로 코르도바 프로젝트, 아이오닉 - 앵귤러 프로젝트에서 아임포트 일반/정기결제 및 본인인증 기능을 이용하실 수 있습니다. 아이오닉 - 리액트 프로젝트는 [iamport-capacitor](https://github.com/iamport/iamport-capacitor) 플러그인을 사용하시면 됩니다. 자세한 내용은 아래 매뉴얼을 참고해주세요.

- [코르도바 프로젝트에서 아임포트 연동하기](https://github.com/iamport/iamport-cordova/blob/master/README.md)
- [아이오닉 - 앵귤러 프로젝트에서 아임포트 연동하기](https://github.com/iamport/iamport-cordova/blob/master/manuals/IONIC.md)
- [아이오닉 - 리액트 프로젝트/캐패시터 프로젝트에서 아임포트 연동하기](https://github.com/iamport/iamport-capacitor/blob/master/README.md)

____________________________________________________________________

Ionic 환경에서 아임포트 결제모듈을 쉽게 연동하기 위한 Ionic Cordova 플러그인입니다.  
외부주소로의 redirection이 포함되어있어 InAppBrowser를 활용하며, 외부주소에서 다시 앱 복귀를 위해 Custom URL Scheme을 사용합니다.

## Required

- [Custom URL scheme](https://github.com/EddyVerbruggen/Custom-URL-scheme)
- [InAppBrowser](https://github.com/apache/cordova-plugin-inappbrowser)

## Install

cordova plugin add https://github.com/iamport/iamport-ionic --variable URL_SCHEME=**ioniciamport**

플러그인명세(plugin.xml)에 dependency가 정의되어있기 때문에 iamport-ionic을 설치하면 Custom URL Scheme 플러그인과 InAppBrowser 플러그인이 설치됩니다.  
Custom URL Scheme 플러그인 설치를 위해 URL_SCHEME파라메터를 전달받습니다. 앱에서 사용하실 고유한 URL Scheme값을 지정하시면 됩니다.  

## Usage
### 1. javascript 선언  
플러그인 설치가 되면 ng-cordova-iamport.js가 platform 폴더에 자동으로 복사가 됩니다.  
때문에, ionic 기본 페이지인 index.html에서 script를 선언만 해주시면 됩니다. 
(단, `app.js`, `controllers.js` 보다 앞에 추가해주셔야 `ng-cordova-iamport.js`가 제공하는 angular module과 factory 사용이 가능합니다.  

```html
<script src="js/ng-cordova-iamport.js"></script>
```

### 2. use module (`ngCordovaIamport`)  

```javascript
angular.module('starter.controllers', ['ngCordovaIamport'])
```
### 3. inject factory(`$cordovaIamport`) & call `payment` function

```javascript
angular.controller('SomethingCtrl', function($scope, $http, $cordovaIamport) {
	
	$scope.checkout = function() {
		//do something
		
		//결제시작
		var iamport_user_code = 'imp12345678'; // https://admin.iamport.kr에 가입 후 발급
		var param = {
			pay_method : 'card',
			merchant_uid : 'my_service_oid_' + (new Date()).getTime(),
			amount : 1004,
			name : '아이오닉 상품결제',
			buyer_name : '아임포트',
			buyer_email : 'iamport@siot.do',
			buyer_tel : '010-1234-5678',
			app_scheme : 'ioniciamport' //URL_SCHEME과 동일한 값 사용
	    };
	
	    $cordovaIamport.payment(iamport_user_code, param).then(function(result) {
	    	//server에서 결제완료여부 최종 체크할 수 있도록 imp_uid전달
	    	$http.post('/payments/confirm', {imp_uid:result.imp_uid}).then(function(rsp) {
	    		alert(result.imp_uid + '주문이 완료되었습니다.');
	    	}, function(err) {
	    		//do error handling
	    	})
	    }, function(err) {
	    	alert(err);
	    });
	}
	
});
```

## Sample Project  

[iamport-ionic-introduction](https://github.com/iamport/iamport-ionic-introduction) 을 통해 적용된 샘플프로젝트를 확인하실 수 있습니다.


## 카카오페이 결제 후
### 앱으로 돌아 오고나서 이벤트
앱으로 돌아 온 후 결제에 대한 앱 Logic을 등록 해주시면 됩니다.



```javascript
// After Kakaopay payment done
// Event object def:
//{
//  detail: {
//    browser: inAppBrowserRef,  inAppBrowser에 reference
//    url: url,                  불려진 callbackUrl
//    status: status             callbackUrl로 돌아온 결제 상태
//  }
//}
//
//

$document[0].addEventListener('kpay', function (e) {

      if (e && e.detail && e.detail.browser) {
        inAppBrowserRef.addEventListener('loadstop', function (event) {
          e.detail.browser.close();
        });
      }

       // Application KakaoPay handling logic goes here
       // ...
       // ...

}, false);

```
