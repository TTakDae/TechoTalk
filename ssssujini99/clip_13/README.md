# Stream ์ด๋?

๐๐ปโโ๏ธ ๋ชฉ์ฐจ : ์ ์, ํน์ง, ์ฌ์ฉ๋ฒ, ์์, ์ฅ๋จ์ 

---

![img.png](img.png)

## ๐ก 1. ์ ์

### ์ ์1 : ๋ฐ์ดํฐ ์ฒ๋ฆฌ ์ฐ์ฐ์ ์ง์ํ๋๋ก ์์ค์์ ์ถ์ถ๋ **์ฐ์**๋ ๊ฐ ์์

### ์ ์2 : ์ปฌ๋ ์์ ์์๋ฅผ ํ๋์ฉ ์ฐธ์กฐํด ๋๋ค์์ผ๋ก ์ฒ๋ฆฌํ  ์ ์๋ ๋ฐ๋ณต์

### ์์ฝ : ๋ฐ์ดํฐ ์ปฌ๋ ์ ๋ฐ๋ณต์ ๋ฉ์ง๊ฒ ์ฒ๋ฆฌํ๋ ๊ธฐ๋ฅ

### ์๋ฐ์ ์คํธ๋ฆผ API : ์ด ๋ฐ์ดํฐ ์ปฌ๋ ์์ ์ด๋ป๊ฒ ๋ค๋ฃฐ ๊ฒ์ธ๊ฐ๋ฅผ ๋ผํ๋ ์ผ์ข์ "ํ์ดํ๋ผ์ธ"



## ๐ก 2. ํน์ง

![img_1.png](img_1.png)

* ์คํธ๋ฆผ ์ด์  : ์คํธ๋ฆผ์ ์ฐ๊ธฐ ์ ์๋ **how** ์ค์ฌ์ ์ธ๋ถ๋ฐ๋ณต์ ์ด์ฉํจ
* ์คํธ๋ฆผ ์ดํ : **what** ์ค์ฌ์ ๋ด๋ถ ๋ฐ๋ณต์ ์ฐ๊ฒ ๋๋ฉด์ **"์ง๊ด์ ์ธ"** ์ฝ๋๋ฅผ ์งค ์ ์๊ฒ๋จ


```
// ๋นจ๊ฐ์ ์ฌ๊ณผ ํํฐ๋ง
List<Apple> redApples = forEach(appleList, (Apple apple) -> apple.getColor().equals("RED"));

// ๋ฌด๊ฒ ์์๋๋ก ์ ๋ ฌ
redApples.sort(Comparator.comparing(Apple::getWeight));

// ์ฌ๊ณผ ๊ณ ์ ๋ฒํธ ์ถ๋ ฅ
List<Integer> redHeavyAppleUid = new ArrayList<>();
for (Apple apple : redApples)
    redHeavyAppleUid.add(apple.getUidNum());
```



```
List<Integer> redHeavyAppleUid = appleList.stream()
        .filter(apple -> apple.getColor().equals("RED"))        // ๋นจ๊ฐ์ ์ฌ๊ณผ ํํฐ๋ง
        .sorted(Comparator.comparing(Apple::getWeight))         // ๋ฌด๊ฒ ์์๋๋ก ์ ๋ ฌ
        .map(Apple::getUidNum).collect(Collectors.toList());    // ์ฌ๊ณผ ๊ณ ์ ๋ฒํธ ์ถ๋ ฅ
```

---


> ์๋ฃ๊ตฌ์กฐ๊ฐ ํฌํจํ๋ ๋ชจ๋  ๊ฐ์ ๋ฉ์๋์ ํฌํจํ๋ ์ปฌ๋ ์๊ณผ ๋ค๋ฅด๊ฒ,   
> ์คํธ๋ฆผ์ **"์์ฒญํ  ๋๋ง"** ์์๋ฅผ ๊ณ์ฐํ๋ ๊ณ ์ ๋ ์๋ฃ๊ตฌ์กฐ๋ฅผ ๊ฐ์ง๋ค.

> ์ ๊ทน์  ์์ฑ : ๋ชจ๋  ๊ฐ์ "๋ค" ์ฒ๋ฆฌํ๊ณ  ๋๊ฒจ์ค   

> **๊ฒ์ผ๋ฅธ ์์ฑ : ์์ฒญํ  ๋๋ง๋ค ๊ฐ์ ์ฒ๋ฆฌํ๊ณ  ๋๊ฒจ์ค** 


--> ์ฌ๋ฌ ๊ฐ์ ์กฐ๊ฑด์ด ์ค์ฒฉ๋ ์ํฉ์์ ๊ฐ์ด ๊ฒฐ์ ๋๋ฉด,   
    ๋ถํ์ํ ์ฐ์ฐ์ ์งํํ์ง ์๊ณ  ์กฐ๊ฑด๋ฌธ์ ๋น ์ ธ๋์ ์คํ ์๋๋ฅผ ๋์ธ๋ค.


### โ ์ถ๊ฐ > ์ปฌ๋ ์ vs. ์คํธ๋ฆผ


> ๋ฐ์ดํฐ๋ฅผ ๊ณ์ฐํ๋ ์๊ธฐ

์ปฌ๋ ์์ ํ์ฌ ์๋ฃ๊ตฌ์กฐ๊ฐ ํฌํจํ๋ ๋ชจ๋  ๊ฐ์ ๋ฉ๋ชจ๋ฆฌ์ ์ ์ฅํ๋ ์๋ฃ๊ตฌ์กฐ๋ค.   
์ฆ, ์ปฌ๋ ์์ ๋ชจ๋  ์์๋ ์ปฌ๋ ์์ ์ถ๊ฐํ๊ธฐ ์ ์ ๊ณ์ฐ๋์ด์ผ ํ๋ค.   
์คํธ๋ฆผ์ ์์ฒญํ  ๋๋ง ์์๋ฅผ ๊ณ์ฐํ๋ ๊ณ ์ ๋ ์๋ฃ๊ตฌ์กฐ๋ค.   
์คํธ๋ฆผ์ ์์๋ฅผ ์ถ๊ฐํ๊ฑฐ๋ ์ ๊ฑฐํ  ์ ์๋ค.   
์ฆ, ์ฌ์ฉ์๊ฐ ๋ฐ์ดํฐ๋ฅผ ์์ฒญํ  ๋๋ง ๊ฐ์ ๊ณ์ฐํ๋ค.   

> ๋ฐ๋ณต ํ์

์ปฌ๋ ์์ ๋ฏธ๋ฆฌ ๊ณ์ฐ๋์ด์ง ๊ฐ์ ๋ฉ๋ชจ๋ฆฌ์ ๊ฐ๊ณ  ์๊ธฐ ๋๋ฌธ์ ์ฌ๋ฌ๋ฒ ํ์์ด ๊ฐ๋ฅํ๋ค   
๋ฐ๋ฉด ์คํธ๋ฆผ์ ๋ฐ๋ณต์์ฒ๋ผ ํ ๋ฒ๋ง ํ์ํ  ์ ์๋ค. ์ฆ, ํ์๋ ์คํธ๋ฆผ์ ์์๋ ์๋น๋๋ค.   

> ๋ฐ์ดํฐ ๋ฐ๋ณต ์ฒ๋ฆฌ ๋ฐฉ๋ฒ

์ปฌ๋ ์์ ์ฌ์ฉํ๋ ค๋ฉด ์ฌ์ฉ์๊ฐ ์ง์  ์์๋ฅผ ๋ฐ๋ณตํด์ผํ๋ค. ์ด๋ฅผ ์ธ๋ถ ๋ฐ๋ณต์ด๋ผ ํ๋ค.   
์คํธ๋ฆผ์ ๋ฐ๋ณต์ ์์์ ์ฒ๋ฆฌํ๊ณ  ๊ฒฐ๊ณผ ์คํธ๋ฆผ๊ฐ์ ์ด๋๊ฐ ์ ์ฅํด์ฃผ๋ ๋ด๋ถ ๋ฐ๋ณต์ ์ฌ์ฉํ๋ค.   


## ๐ก 3. ์ฌ์ฉ๋ฒ

### ๊ตฌ์ฑ

![img_2.png](img_2.png)

* ์ฒซ ๋ฒ์งธ : ์ปฌ๋ ์์ ์คํธ๋ฆผ์ผ๋ก ๋ง๋ค์ด์ฃผ๋ ์ฐ์ฐ์ (์ฆ, ์คํธ๋ฆผ ์์ฑ)
* ๋ ๋ฒ์งธ : ๋ฐ์ดํฐ๋ฅผ ์ฒ๋ฆฌํ๋ ์ค๊ฐ ์ฐ์ฐ์
* ์ธ ๋ฒ์งธ : ์ฐ์ฐ์ ์ ๋ฆฌํ๊ณ  ๊ฒฐ๊ณผ๋ฅผ ๋์ถํ๋ ์ต์ข ์ฐ์ฐ์


```
list.stream() //์คํธ๋ฆผ ๋ง๋ค๊ธฐ   
    .distinct() //์ค๊ฐ์ฐ์ฐ   
    .limit(5) //์ค๊ฐ์ฐ์ฐ
    .sorted() //์ค๊ฐ์ฐ์ฐ
    .forEach(System.out::println) //์ต์ข์ฐ์ฐ
```


## ๐ก 4. ์์


```
@Data
public class SampleDto {
    private int idx;
    private String name;
    private String gender;
}
```

* ์คํธ๋ฆผ ๊ธฐ๋ณธ

```
/**
 * ๊ธฐ์กด Java7 ์ฝ๋์ Java8 ์ฝ๋๋ฅผ ๋น๊ตํด๋ณด์.
 */
public class BasicStream {
    public static void main(String[] args) {
        java7Code();
        java8Code();
    }
 
    /**
     * java7
     */
    static void java7Code() {
        /* java7 */
        List<SampleDto> list = new ArrayList<>();
        List<SampleDto> sampleDtoList = new ArrayList<>();
 
        for (SampleDto sampleDto : sampleDtoList) {
            if (sampleDto.getIdx() < 10) {
                list.add(sampleDto);
            }
        }
 
        /* ์ต๋ชํด๋์ค */
        Collections.sort(list, new Comparator<SampleDto>() {
            @Override
            public int compare(SampleDto o1, SampleDto o2) {
                return Integer.compare(o1.getIdx(), o2.getIdx());
            }
        });
 
        /* ์ ๋ ฌ๋ ๋ฆฌ์คํธ ์ค sampleDto์ getName ์ํ */
        List<String> stringList = new ArrayList<>();
        for (SampleDto sampleDto : list) {
            stringList.add(sampleDto.getName());
        }
    }
 
    /**
     * java8
     */
    static void java8Code() {
        /* java8 */
        List<SampleDto> sampleDtoList = new ArrayList<>();
 
        List<String> list = sampleDtoList.stream()
                /** ๋๋ค๋ฅผ ์ธ์๋ก ๋ฐ์, ์คํธ๋ฆผ์์ ํน์  ์์๋ฅผ ์ ์ธ์ํจ๋ค. ์๋๋ idx๊ฐ 10 ์ด์์ธ ๋ฐ์ดํฐ๋ฅผ ์ ํํ๋ค. */
                .filter(d -> d.getIdx() < 10) // idx๊ฐ 10๋ณด๋ค ์์ ๋ฐ์ดํฐ ์ ํ
                .sorted(Comparator.comparing(SampleDto::getIdx)) // idx ์์๋ก ์ ๋ ฌ
                /** ๋๋ค๋ฅผ ์ด์ฉํด์ ํ ์์๋ฅผ ๋ค๋ฅธ ์์๋ก ๋ณํํ๊ฑฐ๋ ์ ๋ณด๋ฅผ ์ถ์ถํ๋ค. */
                .map(SampleDto::getName) // ์ด๋ฆ ์ถ์ถ
                //.limit(3) // ์ ์ฐฉ์ 3๊ฐ๋ง ์ ํ
                .collect(Collectors.toList()); // ๋ฆฌ์คํธ๋ก ์ ์ฅ
 
        /* ๋ณ๋ ฌ */
        List<String> parallelList = sampleDtoList.parallelStream()
                            .filter(d -> d.getIdx() < 10) // idx๊ฐ 10๋ณด๋ค ์์ ๋ฐ์ดํฐ ์ ํ
                            .sorted(Comparator.comparing(SampleDto::getIdx)) // idx ์์๋ก ์ ๋ ฌ
                            .map(SampleDto::getName) // ์ด๋ฆ ์ถ์ถ
                            .collect(Collectors.toList()); // ๋ฆฌ์คํธ๋ก ์ ์ฅ
    }
 
    /**
     * ์คํธ๋ฆผ์ ๋จ ํ๋ฒ๋ง ์๋น ๊ฐ๋ฅํ๋ค.
     */
    static void impossibleNewStream() {
        List<String> title = Arrays.asList("A", "B", "C");
        Stream<String> s = title.stream();
 
        s.forEach(System.out::println); // A, B, C ์ถ๋ ฅ
        /** java.lang.IllegalStateException:์คํธ๋ฆผ์ด ์ด๋ฏธ ์๋น๋์๊ฑฐ๋ ๋ซํ ์๋ฌ ๋ฐ์ */
        s.forEach(System.out::println);
    }
}
```


* stream filter

```
public class StreamFilter {
    public static void main(String[] args) {
 
    }
 
    /**
     * ์ค๋ณต ํํฐ๋ง
     */
    static void distinct() {
        /* ๋ด๋ถ๋ฐ๋ณต */
        List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
 
        numbers.stream()
                .filter(i -> i % 2 == 0)
                .distinct() /* ์ค๋ณต ํํฐ๋ง */
                .forEach(System.out::println);
    }
}
```


* stream ์ธ๋ถ๋ฐ๋ณต/๋ด๋ถ๋ฐ๋ณต

```
/**
 * ์ธ๋ถ๋ฐ๋ณต/๋ด๋ถ๋ฐ๋ณต
 ์ธ๋ถ๋ฐ๋ณต : ์ฌ์ฉ์๊ฐ ์ง์  ์์๋ฅผ ๋ฐ๋ณต (for-each ๋ฑ)
 ๋ด๋ถ๋ฐ๋ณต : ์คํธ๋ฆผ ๋ผ์ด๋ธ๋ฌ๋ฆฌ -๋ฐ๋ณต์ ์์์ ์ฒ๋ฆฌํ๊ณ  ๊ฒฐ๊ณผ ์คํธ๋ฆผ๊ฐ์ ์ด๋๊ฐ์ ์ ์ฅํด์ค๋ค
 */
public class StreamIteration {
    public static void main(String[] args) {
        forEachCode();
    }
 
    /**
     * ๋ด๋ถ๋ฐ๋ณต/์ธ๋ถ๋ฐ๋ณต ์์ 
     */
    static void forEachCode() {
        /* ๋ด๋ถ๋ฐ๋ณต */
        List<String> names = new ArrayList<>();
        List<SampleDto> sampleDtoList = new ArrayList<>();
 
        for (SampleDto sampleDto : sampleDtoList) {
            names.add(sampleDto.getName());
        }
 
        /* ์ธ๋ถ๋ฐ๋ณต */
        List<String> streamNames =  sampleDtoList.stream()
                                        .map(SampleDto::getName)
                                        .collect(Collectors.toList());
    }
}
```



## ๐ก 5. ์ฅ๋จ์ 

> ### ์ฅ์ 
> * ๊ฐ๋์ฑ์ด ์ข๋ค
> * ์ฝ๋ ๋ณ๊ฒฝ์ด ์ฝ๋ค (์ ์ฐ์ฑ์ด ๋๋ค)
> * ๋ณ๋ ฌ์ฒ๋ฆฌ๋ฅผ ๊ฐ๋จํ๊ฒ ํด๊ฒฐํ  ์ ์๋ค (-> ์ฑ๋ฅ ๋์์ง)

> ### ๋จ์ 
> * ์์ฑํ๋ ๋ฐ์ ์ ์ง ์์ ๋น์ฉ์ด ๋ฐ์ํ๋ค