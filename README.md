# Cordova Push Notification Lab #

> 以Android GCM為例,使用Azure Mobile Apps 推播服務

## Azure Notification Hub ##

> 建立Mobile Apps的推播服務(Azure Notification Hub)

1. https://console.developers.google.com/project 建立 Google Cloud Platform 帳戶
2. 選擇 Google API / Google Cloud Messaging for Android / 啟用 API
3. 憑證 / 建立憑證 / API 金鑰 / 伺服器金鑰,這個 API 金鑰值，可以讓 Azure Notification Hub 能夠使用 GCM 進行驗證，傳送推播通知
4. 進到Google Cloud Messaging ，啟用GCM
5. Visual Studio Create Mobile Apps 
6. 發行 Mobile Apps
7. App Services > your Mobile Apps > Setting > Mobile(Push) > Create Notification Hub
8. Azure Notification Hub / 設定 / Google (GCM) / 填入 API 金鑰並儲存

## 安裝 Android Google Play 服務 ##
> Cordova App Push Plugin 程式仰賴 Google Play 服務來進行推播通知

1. 開啟 Visual Studio
2. 按一下 [工具] > [Android] > [Android SDK Manager]
3. 安裝Extras 裡以下中必要的封裝： 

•Android Support Library (版本 23 或更高版本)

•Android Support Repository (版本 20 或更高版本)

•Google Play 服務 (版本 27 或更高版本)

•Google Repository (版本 22 或更高版本)


## Cordova App Push Plugin##

1. Create Cordova App
2. Add Mobile Apps Client Plugin (https://github.com/azure/azure-mobile-apps-cordova-client)
3. Add Push Plugin (https://github.com/phonegap/phonegap-plugin-push) 
4. index.html < meta http-equiv="Content-Security-Policy"... > 使用以下程式碼取代 (http://codeian.idv.tw/ 改為你的Mobile Apps 的網址)

		<meta http-equiv="Content-Security-Policy" content="default-src 'self' data: 
		gap: https://yourmobileapp.azurewebsites.net https://ssl.gstatic.com 'unsafe-eval'; 
		style-src 'self' 'unsafe-inline'; media-src *">

5. 在onDeviceReady()撰寫程式碼如下,讓App啟動後即註冊推播通知

		function onDeviceReady() {
	        // 處理 Cordova 暫停與繼續事件
	        document.addEventListener('pause', onPause.bind(this), false);
	        document.addEventListener('resume', onResume.bind(this), false);
	       
	        //url 須加入.html裡的meta宣告
	        var azureClient = new WindowsAzure
	            .MobileServiceClient('https://yourmobileapp.azurewebsites.net');
	
	
	        var push = PushNotification.init({
	            android: {
	                senderID: "填入Google Project ID"
	            },
	            ios: {
	                alert: "true",
	                badge: "true",
	                sound: "true"
	            },
	            windows: {
				}
	        });
	
	        push.on('registration', function (data) {
	            azureClient.push.register('gcm', data.registrationId);
	        });
	
	        push.on('notification', function (data, d2) {
	            alert('Push Received: ' + data.message);
	        });
	
	        push.on('error', function (e) {
	            console.log(e.message);
	        })
    	};    

6. 部署 Cordova App 到設備上
7. 至 portal.azure.com 管理介面, Azure Notification Hub > Test Send > 選擇 Android,進行推播測試