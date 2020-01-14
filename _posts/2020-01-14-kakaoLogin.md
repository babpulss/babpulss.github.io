---
title: "카카오 REST API 로그인 서비스"
categories:
 - "RESTAPI"
---
카카오 디벨로퍼에서 제공하는 카카오 로그인 서비스를 REST API로 구현하는 방식입니다  

우선 jsp 혹은 html에서 리다이렉트URL로 받을 주소를 작성한 주소가 두개가 있습니다.
리다이렉트URI는 생성한 웹어플리케이션 설정에서 직접 입력해야 합니다.  
대부분 로컬호스트환경에서 실행하실테니 리다이렉트URL을 localhost:8080이 대부분 유저가 가지고 있는 초기값이겠네요.  

첫번째 링크는 일반적인 카카오로그인입니다.  
두번째는 동적동의를 받을 수 있는 로그인 링크입니다.  
동적동의란 처음 로그인할 때 개인정보수집을 체크하지 않은 분은 계속하여 같은 설정으로 로그인하게 되어있는데 그 때 두번째 주소로 접속하게 하면 동적동의를 일으켜서 이메일수집여부를 다시 물을 수 있게 합니다.  
두번째 주소의 scope값에 여러가지 항목이 들어있는데 설정에서 수집허용체크를 한 후 저 항목들을 기재하면 접속한 사용자에게 동적동의를 할 것인지 묻게 되는 것이지요  

```html
<a href="https://kauth.kakao.com/oauth/authorize?client_id=key&
	redirect_uri=http://localhost:8080/login&response_type=code"><img src="/resources/login.png"></a>

<a href="https://kauth.kakao.com/oauth/authorize?client_id=key&	redirect_uri=http://localhost:8080/login&response_type=code&scope=account_email,gender,age_range,birthday">이메일 동의 로그인</a>
```

위의 주소로 들어가면 get방식으로 code를 부여받습니다  
그 코드를 바탕으로 엑세스 토큰을 다시 전송받아야 합니다  
```java
	public String getAccessToken(String code) {
		try {
			URL url = new URL("https://kauth.kakao.com/oauth/token");
			HttpURLConnection con = (HttpURLConnection) url.openConnection();

			con.setRequestMethod("POST");
			con.setDoOutput(true);

			BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(con.getOutputStream()));
			StringBuilder sb = new StringBuilder();
			sb.append("grant_type=authorization_code");
			sb.append("&client_id=key");
			sb.append("&redirect_uri=http://설정한 redirec_uri/login");
			sb.append("&code=" + code);
			bw.write(sb.toString());
			bw.flush();

			int response = con.getResponseCode();
			System.out.println("response code : " + response); // 200인 경우 정상적인 통신이 이루어짐

			BufferedReader br = new BufferedReader(new InputStreamReader(con.getInputStream()));
			String read;
			StringBuilder result = new StringBuilder();

			while ((read = br.readLine()) != null) {
				result.append(read);
			}

			JSONObject json = new JSONObject(result.toString());
			String accessToken = json.getString("access_token");
			String refreshToken = json.getString("refresh_token");

			System.out.println(accessToken);
			System.out.println(refreshToken);

			bw.close();
			br.close();
			return accessToken;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return null;
	}
```

받은 액세스 토큰으로 해당 유저의 정보를 다시 요청합니다  

```java
	public void getUserInfo(String accessToken) {
		try {
			dynamicAgreement(accessToken);
			URL url = new URL("https://kapi.kakao.com/v2/user/me");

			HttpURLConnection con = (HttpURLConnection) url.openConnection();
			con.setRequestMethod("POST");
			con.setRequestProperty("Authorization", "Bearer " + accessToken);
			con.setRequestProperty("Content-type", "application/x-www-form-urlencoded;charset=utf-8");
			
			int responseCode = con.getResponseCode();
			System.out.println("responseCode : " + responseCode);
			
			BufferedReader br = new BufferedReader(new InputStreamReader(con.getInputStream()));
			
			String line;
			String result = "";
			
			while ((line = br.readLine()) != null) {
				result += line;
			}
			System.out.println(result);
			
			br.close();
			con.disconnect();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
```

수신한 액세스토큰은 저장하였다가 로그아웃이나 다른 기능에 이용할 수 있습니다

```java
	// 액세스 토큰을 통해 로그아웃을 처리
	public void logout(String accessToken) {
		try {
			URL url = new URL("https://kapi.kakao.com/v1/user/logout");
			HttpURLConnection con = (HttpURLConnection) url.openConnection();
			
			con.setRequestMethod("POST");
			con.setRequestProperty("Authorization", "Bearer " + accessToken);
			
			BufferedReader br = new BufferedReader(new InputStreamReader(con.getInputStream()));
			
			int response = con.getResponseCode();
			System.out.println(response);
			
			String read;
			String result = "";
			
			while ((read = br.readLine()) != null) {
				result += read;
			}
			System.out.println(result);
			
			br.close();
			con.disconnect();
		} catch(Exception e) {
			e.printStackTrace();
		}
	}
```