---
title: "Set Selenium"
categories:
 - "selenium"
---
셀레니움을 사용하기 위해 셀레니움 jar파일과 의존성을 갖는 기타 jar파일을 모두 빌드패스에 추가해줍니다.  

[1](/assets/img/setSelenium/1.png)

추가로 크롬드라이버의 설정값을 편하게 작성하기 위해 생성된 자바프로젝트에 놓았습니다.

[2](/assets/img/setSelenium/2.png)


셀레니움을 연습하기 위한 사이트로는 <http://the-internet.herokuapp.com/>를 대상으로 하겠습니다.
셀레니움 설명사이트는 <https://www.guru99.com/>을 참조하였습니다.


```java
public class Sele1 {
	public static void main(String[] args) {
		
		// 크롬드라이버 경로 설정 및 셀레니움을 사용하기 위한 인스턴스 생성
    	System.setProperty("webdriver.chrome.driver","chromedriver.exe");
		WebDriver driver = new ChromeDriver();
    	
        String baseUrl = "http://the-internet.herokuapp.com/";
        String expectedTitle = "The Internet";
        String actualTitle = "";

        // 할당한 주소를 셀레니움으로 접속하기
        driver.get(baseUrl);

        // 실제 접속할 사이트의 타이틀 얻기
        actualTitle = driver.getTitle();

        // 예상되는 타이틀제목과 맞는다면 콘솔에 "일치"를 출력
        if (actualTitle.contentEquals(expectedTitle)){
            System.out.println("일치");
        } else {
            System.out.println("불일치");
        }
       
        // 드라이버 종료
        driver.close();
	}

}
```

실행결과로는 다음과 같습니다.

[3](/assets/img/setSelenium/3.png)

타이틀이 제가 미리 입력해놓은 expectedTitle과 같기 때문에 일치함을 살펴볼 수 있습니다.  
이미 눈치채신 분들도 계시겠지만 위와 같이 접속한 사이트의 정보들을 다양한 메서드를 통해 얻어올 수 있으며 마음대로 자료를 가공하고 편집할 수 있음을 살필 수 있습니다.  


WebDriver클래스가 가진 유용한 메서드들은 다음과 같습니다.

.getPageSource() : 접속한 사이트의 html소스를 String값으로 가져옵니다.
.findElement() : 단연코 셀레니움에서 가장 많이 쓰일 메서드일 것입니다. html소스에서 요소를 검색합니다. 검색된 요소를 클릭하거나 값을 넣을 수도 있게 되는 것이죠.  
.findElements() 역시 같은 기능을 수행하지만 List형식으로 반환하여 2개 이상의 요소를 탐색할 때 쓰입니다. 
대표적으로 id를 기준으로 검색할 때는 .findElement()가 쓰일 테며 클래스를 기준으로 검색한다면 .findElements()가 쓰여야 하겠죠.
하지만 2개 이상의 요소도 .findElement()로 검색해도 무방합니다.  
다만 요소의 검색결과가 2개 이상일 경우 가장 처음에 검색된 요소를 지목하게 됩니다.  

.findElement() 혹은 .findElements()의 인자값으로는 By라는 인스턴스가 들어갑니다.  
따라서 자연스럽게 
```java
driver.findElement(By.id("찾으려는 id값")); 
```
과 같은 형식을 취하게 됩니다.  

다음은 By클래스가 가지고 있는 메서드 중에서 유용하게 쓰일만한 메서드입니다.  


| 메서드 명	| 기능설명 |	예시 |
|---|:---:|---:|
|`By.id`	| id명을 기준으로 요소를 탐색 | findElement(By.id("someId"))  |
|`By.className`| 클래스명을 기준으로 요소를 탐색 | findElement(By.className("someClassName"))|
|`By.cssSelector`| css선택자와 동일한 표현식으로 요소를 탐색 |	findElement(By.cssSelector("input#email"))|
|By.linkText|	화면에 출력된 단어를 기준으로 탐색 | findElement(By.linkText("A/B Testing"))  |
|By.partialLinkText	| 출력되는 단어 중에 해당하는 단어가 포함된 모든 요소를 탐색 | findElement(By.partialLinkText("Testing"))  |
|By.name	| name속성으로 요소를 탐색 | findElement(By.name("someName"))  |
|By.tagName	| 태그네임으로 탐색	| findElement(By.tagName("div"))  |
|By.xpath	| xpath를 이용한 탐색 | findElement(By.xpath("//html/body/div/table/tbody/tr/td[2]/table/ tbody/tr[4]/td/table/tbody/tr/td[2]/table/tbody/tr[2]/td[3]/ form/table/tbody/tr[5]")) | 


보편적으로 제일 많이 쓰일 요소는 아무래도 id로 탐색이거나 cssSelector 검색으로 보입니다.
다만 이도저도 방법이 생각나지 않는다면 xpath로 탐색하는 방법이 있을 것입니다.
xpath값을 얻는 방법은 간단합니다.

[4](/assets/img/setSelenium/4.png)

크롬의 개발자 도구에서 요소를 선택해 마우스 오른쪽 버튼을 누르면 나타나는 메뉴 중에서 xpath주소를 복사할 수 있는데요.  
이 값을 통해 탐색을 시도한다면 셀레니움에서 요소를 쉽게 탐색할 수 있을 것입니다.


