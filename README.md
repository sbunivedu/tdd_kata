# TDD Katas

Here is a practical, step-by-step example of Test-Driven Development (TDD) using the popular String Calculator Kata. We will implement a StringCalculator that sums comma-separated numbers using the strict TDD Red-Green-Refactor cycle. [1, 2, 3] 

## Prerequisites
Make sure you have JUnit 5 added to your Maven pom.xml: [4] 

```java
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.11.0</version>
    <scope>test</scope>
</dependency>
```

## 🔴 Step 1: The Red Phase (Empty String)
First, write a test for the simplest possible scenario: passing an empty string should return 0.

```java
StringCalculatorTest.java

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

class StringCalculatorTest {
    @Test
    void add_emptyString_returnsZero() {
        StringCalculator calculator = new StringCalculator();
        assertEquals(0, calculator.add(""));
    }
}
```

Run the test. It fails to even compile because StringCalculator doesn't exist yet.

## 🟢 Step 2: The Green Phase
Write the absolute minimum code to make the test pass. Do not implement any real logic yet. [5, 6] 

```java
StringCalculator.java

public class StringCalculator {
    public int add(String numbers) {
        return 0; 
    }
}
```

Run the test. It passes!

## 🔴 Step 3: The Red Phase (Single Number)
Now write a test for a single number. Passing "1" should return 1.

```java
StringCalculatorTest.java

@Test
void add_singleNumber_returnsThatNumber() {
    StringCalculator calculator = new StringCalculator();
    assertEquals(1, calculator.add("1"));
}
```

Run the test. It fails because our production code is hardcoded to return 0.

## 🟢 Step 4: The Green Phase
Update the production code with the simplest logic to pass both tests. [6] 

```java
StringCalculator.java

public class StringCalculator {
    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }
        return Integer.parseInt(numbers);
    }
}
```

Run the tests. Both pass!

## 🔴 Step 5: The Red Phase (Two Numbers)
Write a test for two comma-separated numbers. Passing "1,2" should return 3.

```java
StringCalculatorTest.java

@Test
void add_twoNumbersCommaSeparated_returnsSum() {
    StringCalculator calculator = new StringCalculator();
    assertEquals(3, calculator.add("1,2"));
}
```

Run the test. It fails with a NumberFormatException because "1,2" cannot be parsed directly as an integer.

## 🟢 Step 6: The Green Phase
Introduce the splitting logic to sum the numbers. [2] 

```java
StringCalculator.java

public class StringCalculator {
    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }
        String[] tokens = numbers.split(",");
        int sum = 0;
        for (String token : tokens) {
            sum += Integer.parseInt(token);
        }
        return sum;
    }
}
```

Run the tests. All three tests pass!

## 🔵 Step 7: The Refactor Phase
Now that we are in a safe "Green" state, we clean up the code without changing its behavior. Let's refactor the test file to avoid re-instantiating StringCalculator in every test, and clean up the production loop using Java Streams. [3, 7] 

```java
Refactored StringCalculatorTest.java

import org.junit.jupiter.api.BeforeEach;import org.junit.jupiter.api.Test;import static org.junit.jupiter.api.Assertions.assertEquals;
class StringCalculatorTest {
    private StringCalculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new StringCalculator();
    }

    @Test
    void add_emptyString_returnsZero() {
        assertEquals(0, calculator.add(""));
    }

    @Test
    void add_singleNumber_returnsThatNumber() {
        assertEquals(1, calculator.add("1"));
    }

    @Test
    void add_twoNumbersCommaSeparated_returnsSum() {
        assertEquals(3, calculator.add("1,2"));
    }
}
```

Refactored StringCalculator.java

```java
import java.util.Arrays;
public class StringCalculator {
    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }
        return Arrays.stream(numbers.split(","))
                .mapToInt(Integer::parseInt)
                .sum();
    }
}
```

Run all tests again. They still pass, proving our refactoring didn't break anything!

[1] [https://www.youtube.com](https://www.youtube.com/watch?v=YF_ayk_rV1Y)

[2] [https://www.statuscake.com](https://www.statuscake.com/blog/serving-code-katas-using-test-driven-development/)

[3] [https://www.browserstack.com](https://www.browserstack.com/guide/tdd-in-java)

[4] [https://github.com](https://github.com/qty-playground/tdd-kata-java)

[5] [https://javacodehouse.com](https://javacodehouse.com/blog/test-driven-development-tutorial/)

[6] [https://www.baeldung.com](https://www.baeldung.com/java-test-driven-list)

[7] [https://www.youtube.com](https://www.youtube.com/watch?v=5PH3VSj_I1k)


## Kata 1: Extending the String Calculator (Handling Newlines)
Let's add the requirement to handle newlines as a delimiter alongside commas (e.g., "1\n2,3" should return 6).

## 🔴 Step 1: The Red Phase
Add a test case to handle newlines to your existing test file.

```java
StringCalculatorTest.java

@Test
void add_numbersSeparatedByNewlinesOrCommas_returnsSum() {
    assertEquals(6, calculator.add("1\n2,3"));
}
```

Run the test. It fails with a NumberFormatException because "1\n2" isn't a valid integer.

## 🟢 Step 2: The Green Phase
Modify the stream split regex to match either a comma or a newline character ([\n,]).

```java
StringCalculator.java

import java.util.Arrays;
public class StringCalculator {
    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }
        // Split on either comma OR newline
        return Arrays.stream(numbers.split(",|\n"))
                .mapToInt(Integer::parseInt)
                .sum();
    }
}
```

Run the tests. All tests pass!

## 🎳 Kata 2: The Bowling Game Kata
The goal of this kata is to calculate the score of a 10-frame bowling game based on a series of rolls.

* Strike: If you knock down 10 pins on the first roll, the frame ends. The score is 10 + the pins knocked down on the next two rolls.
* Spare: If you knock down 10 pins across two rolls, the score is 10 + the pins knocked down on the next single roll.

## 🔴 Step 1: The Red Phase (Gutter Game)
We'll start with a "gutter game" where the player rolls 20 zeros.

```java
BowlingGameTest.java

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;
class BowlingGameTest {
    private BowlingGame game;

    @BeforeEach
    void setUp() {
        game = new BowlingGame();
    }

    private void rollMany(int n, int pins) {
        for (int i = 0; i < n; i++) {
            game.roll(pins);
        }
    }

    @Test
    void score_gutterGame_returnsZero() {
        rollMany(20, 0);
        assertEquals(0, game.score());
    }
}
```

Compile error. We need to create the class and its method stubs.

## 🟢 Step 2: The Green Phase
Write the minimum code to compile and pass the gutter game test.

```java
BowlingGame.java

public class BowlingGame {
    public void roll(int pins) {
    }

    public int score() {
        return 0;
    }
}
```

Run the test. It passes.

## 🔴 Step 3: The Red Phase (All Ones)
Let's test hitting 1 pin on every single roll (20 rolls total). The expected score is 20.

```java
BowlingGameTest.java

@Test
void score_allOnes_returnsTwenty() {
    rollMany(20, 1);
    assertEquals(20, game.score());
}
```

Run the test. It fails because score() is hardcoded to 0.

## 🟢 Step 4: The Green Phase
Track the total score by summing up the pins as they are rolled.

```java
BowlingGame.java

public class BowlingGame {
    private int score = 0;

    public void roll(int pins) {
        score += pins;
    }

    public int score() {
        return score;
    }
}
```

Run the tests. Both tests pass.

## 🔴 Step 5: The Red Phase (One Spare)
Now we introduce game logic. If a player rolls a spare (e.g., 5 then 5), the next roll (e.g., 3) counts twice.

```java
BowlingGameTest.java

@Test
void score_oneSpare_addsNextRollBonus() {
    game.roll(5);
    game.roll(5); // Spare!
    game.roll(3);
    rollMany(17, 0);
    // Score should be: (10 + 3) + 3 = 16
    assertEquals(16, game.score());
}
```

Run the test. It fails and yields 13 instead of 16 because we don't have bonus logic yet.

## 🟢 Step 6: The Green Phase
To calculate complex bonuses like strikes and spares, we need to shift our strategy from summing rolls on the fly to iterating through the game frame by frame. First, store all rolls in an array.

```java
BowlingGame.java

public class BowlingGame {
    private final int[] rolls = new int[21];
    private int currentRoll = 0;

    public void roll(int pins) {
        rolls[currentRoll++] = pins;
    }

    public int score() {
        int score = 0;
        int frameIndex = 0;
        for (int frame = 0; frame < 10; frame++) {
            if (rolls[frameIndex] + rolls[frameIndex + 1] == 10) { // Spare
                score += 10 + rolls[frameIndex + 2];
                frameIndex += 2;
            } else {
                score += rolls[frameIndex] + rolls[frameIndex + 1];
                frameIndex += 2;
            }
        }
        return score;
    }
}
```

Run the tests. All three pass!

## 🔵 Step 7: The Refactor Phase
Let's tidy up BowlingGame.java by extracting helper methods to make the domain logic clean and highly readable.

```java
Refactored BowlingGame.java

public class BowlingGame {
    private final int[] rolls = new int[21];
    private int currentRoll = 0;

    public void roll(int pins) {
        rolls[currentRoll++] = pins;
    }

    public int score() {
        int score = 0;
        int frameIndex = 0;
        for (int frame = 0; frame < 10; frame++) {
            if (isSpare(frameIndex)) {
                score += 10 + spareBonus(frameIndex);
                frameIndex += 2;
            } else {
                score += sumOfPinsInFrame(frameIndex);
                frameIndex += 2;
            }
        }
        return score;
    }

    private boolean isSpare(int frameIndex) {
        return rolls[frameIndex] + rolls[frameIndex + 1] == 10;
    }

    private int spareBonus(int frameIndex) {
        return rolls[frameIndex + 2];
    }

    private int sumOfPinsInFrame(int frameIndex) {
        return rolls[frameIndex] + rolls[frameIndex + 1];
    }
}
```

Run the tests. They pass perfectly.

