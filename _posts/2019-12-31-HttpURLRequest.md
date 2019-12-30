---
title: "HttpURLRequest"
categories:
 - "restapi"
---
HttpURLRequest클래스 사용하여 REST API를 사용할 수 있습니다.  
REST API를 사용하기 위한 예제 사이트로는 <https://jsonplaceholder.typicode.com/albums>입니다.

참고영상은 <https://www.youtube.com/watch?v=qzRKa8I36Ww>입니다.  
코드도 위의 영상과 다른 게 없는데다 위의 영상에서는 자바11버전에서 제공하는 방법도 같이 소개하고 있으므로 위의 영상을 추후 참조하시면 더 좋지 않을까 생각합니다.

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class JsonPlaceHolder {

	private static HttpURLConnection con;

	public static void main(String[] args) {
		String line;
		BufferedReader reader;
		StringBuffer responseContent = new StringBuffer();
		try {
			URL url = new URL("https://jsonplaceholder.typicode.com/albums/1");
			con = (HttpURLConnection) url.openConnection();
			// Request Setup
			con.setRequestMethod("GET");
			con.setConnectTimeout(5000);
			con.setReadTimeout(5000);

			int status = con.getResponseCode();
			System.out.println(status);
			if (status > 299) {
				reader = new BufferedReader(new InputStreamReader(con.getInputStream()));
				while ((line = reader.readLine()) != null) {
					responseContent.append(line);
				}
				reader.close();
			} else {
				reader = new BufferedReader(new InputStreamReader(con.getInputStream()));
				while ((line = reader.readLine()) != null) {
					responseContent.append(line);
				}
				reader.close();
			}
			System.out.println(responseContent);
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			con.disconnect();
		}
	}
}
```

위의 방식대로 예제 사이트에서 제공하는 album자료를 콘솔에 출력한 것을 볼 수 있습니다.