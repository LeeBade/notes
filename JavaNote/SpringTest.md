
# 测试

## JUnit

org.junit.jupiter.api.Test提供@Test注解，对测试方法使用@Test注解

`import static org.junit.jupiter.api.Assertions.*;`提供断言
- assertEquals(expected, actual)
- assertTrue(): 期待结果为true

- assertFalse(): 期待结果为false

- assertNotNull(): 期待结果为非null

- assertArrayEquals(): 期待结果为数组并与期望数组每个元素的值均相等

- assertEquals(double expected, double actual, double delta)

## Fixture

假设一个类需要对多个方法进行单元测试，每个方法内部都进行声明初始化同一个对象时，可使用Fixture
- Fixture：每次测试前准备、测试后清理的固定代码，避免在每个`@Test`方法开始前书写相同的初始化方法
  - 注解`@BeforeEach、@AfterEach`

```JAVA

public class CalculatorTest {

    Calculator calculator;

    @BeforeEach
    public void setUp() {
        this.calculator = new Calculator();
    }

    @AfterEach
    public void tearDown() {
        this.calculator = null;
    }

    @Test
    void testAdd() {
        assertEquals(100, this.calculator.add(100));
        assertEquals(150, this.calculator.add(50));
        assertEquals(130, this.calculator.add(-20));
    }

    @Test
    void testSub() {
        assertEquals(-100, this.calculator.sub(100));
        assertEquals(-150, this.calculator.sub(50));
        assertEquals(-130, this.calculator.sub(-20));
    }
}

```


- 对于耗时且能共用的资源，可在所有测试开始前准备，所有测试后清理的固定代码
  - 注解`@BeforeAll、@AfterAll`
  - 在所有`@Test`方法运行前后仅运行一次，只能初始化静态变量
  - 例如数据库连接、文件句柄
```JAVA

public class DatabaseTest {
    static Database db;

    @BeforeAll
    public static void initDatabase() {
        db = createDb(...);
    }
    
    @AfterAll
    public static void dropDatabase() {
        ...
    }
}
```

## 异常测试


若某个方法抛出异常，则应该对该异常进行测试


```JAVA
@Test
void testNegative() {
    assertThrows(IllegalArgumentException.class, new Executable() {
        @Override
        public void execute() throws Throwable {
            //被测试方法
        }
    });
}


//上述匿名类可更换为lambda
@Test
void testNegative() {
    assertThrows(IllegalArgumentException.class, ()->{
      // 被测试方法
    }
}

```

## 条件测试

`@Disabled`：在运行测试时排除特定的@Test方法，而不是直接注释
```java

@Disabled
@Test
void testBug101() {
    // 这个测试始终不会运行，但是会被识别
}

```

`@EnableOnOs()/@DisabledOnOs()`：条件测试判断
```JAVA

@Test
@EnabledOnOs(OS.WINDOWS)
void testWindows() {
    assertEquals("C:\\test.ini", config.getConfigFile("test.ini"));
}

@Test
@EnabledOnOs({ OS.LINUX, OS.MAC })
void testLinuxAndMac() {
    assertEquals("/usr/local/test.cfg", config.getConfigFile("test.cfg"));
}

```

`@EnabledIfEnvironmentVariable`

```JAVA

@Test
@EnabledIfEnvironmentVariable(named = "DEBUG", matches = "true")
void testOnlyOnDebugMode() {
    // TODO: this test is only run on DEBUG=true
}

```

## 参数化测试

@ParameterizedTest：标注该方法需要传入参数，分离测试数据和测试代码

分离方法一：测试方法仅一个参数
```JAVA
// 测试方法将被调用四次
@ParameterizedTest
@ValueSource(ints = { 0, 1, 5, 100 })
void testAbs(int x) {
    assertEquals(x, Math.abs(x));
}
```

分离方法二：测试方法有多个参数
- `@MethodSource`注解：允许编写一个同名的静态方法来提供测试参数
```JAVA
@ParameterizedTest
@MethodSource
void testCapitalize(String input, String result) {
    assertEquals(result, StringUtils.capitalize(input));
}

static List<Arguments> testCapitalize() {
    return List.of( // arguments:
            Arguments.of("abc", "Abc"), //
            Arguments.of("APPLE", "Apple"), //
            Arguments.of("gooD", "Good"));
}

```


分离方法三：测试方法有多个参数
- ` @CsvSource`：每一个字符串表示一行，一行包含的若干参数用,分隔
```JAVA
@ParameterizedTest
@CsvSource({ "abc, Abc", "APPLE, Apple", "gooD, Good" })
void testCapitalize(String input, String result) {
    assertEquals(result, StringUtils.capitalize(input));
}

```
- 若需要测试很多次，则可以将测试数据写入CSV文件，并使用`@CsvFileSource`注解读取
  - 注意CSV文件应放在classpath中（test目录下），JUnit只在classpath中查找指定的CSV文件
```JAVA

@ParameterizedTest
@CsvFileSource(resources = { "/test-capitalize.csv" })
void testCapitalizeUsingCsvFile(String input, String result) {
    assertEquals(result, StringUtils.capitalize(input));
}
```