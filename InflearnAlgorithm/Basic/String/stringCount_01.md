# InflearnAlgorithm

```java
public class stringCount_01 {

    public static void main(String args[]) throws IOException {
        stringCount_01 main = new stringCount_01();

        Scanner scanner = new Scanner(System.in);

        String str = scanner.next();

        char targetChar = scanner.next().charAt(0);

        System.out.println(main.solution(str,targetChar));
    }

    public static int solution(String str,char targetChar){

        str = str.toUpperCase(Locale.ROOT);
        //str을 전부 대문자로 만듦

        targetChar = Character.toUpperCase(targetChar);
        //target을 대문자로 만듦

        int count = 0;

        for(int i = 0; i < str.length(); i++){
            if(targetChar == str.charAt(i))
                count++;
        }

        return count;

    }

}
```

* Scanner 사용은 scanner.next(); 로 다음 라인 받아오기
* scanner.charAt();으로 특정 문자 찾기
* str.toUpperCase();로 대문자로 변환 가능
* 테스트 돌릴때는 public 뺀 `class Main`으로 돌리기

```java
for(char x : str.toCharArray()){
            if(targetChar == x)
                count++;
        }
```

* for-each구문을 사용한 예시. for-each에는 반드시 배열 또는 iterator가 봐야 한다.
* str.toCharArray()로 문자열을 char배열로 만들 수 있다.
