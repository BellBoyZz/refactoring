## Refactoring


## WordCount

From Sorawichaya's Readability code: https://github.com/b6210545602/PA4

### countSentence() Method

In the `src/readability/strategy/Counter.java` class

https://github.com/b6210545602/PA4/blob/master/src/readability/strategy/Counter.java

consider this code:

```java
    public int countSentence(String line) {
        int sentencegroups = 0;
        String[] lines = line.split("\\s+");
        for(String word: lines) {
            // the word end with ".", ";", "!", "?"
            if (word.endsWith(".") || word.endsWith(";") || word.endsWith("!") || word.endsWith("?")) {
                String subword = word.substring(0, word.length() - 1);
                if (isNumeric(subword)) sentencegroups--;
                sentencegroups++;
            }
        } // end of the line

        if (sentencegroups == 0) nosentence = true;
        // if there is not a sentence in line 
        // and next line is blank line, count as a sentence
        if (nosentence && line.isBlank()) {
            nosentence = false; 
            sentencegroups++;
        }
        return sentencegroups;
    }
```
* Refactoring Signs:
  - the "if" expression is long and complex.
  - many calls to string.endsWith() when really it just looks for a char.

* Coding Style Error:
  - have some Typos such as "sentencegroups" and "subword".
  - has no space between "for" and "("

* Refactoring: introduce named constant for the punctuation.

```java
    public int countSentence(String line) {
        int sentence_groups = 0;
        String[] lines = line.split("\\s+");
        final String punctuation = ".;!?";
        for (String word: lines) {
            // the word end with ".", ";", "!", "?"
            if (word.endsWith(punctuation)) {
                String sub_word = word.substring(0, word.length() - 1);
                if (isNumeric(sub_word)) sentence_groups--;
                sentence_groups++;
            }
        } // end of the line

        if (sentence_groups == 0) nosentence = true;
        // if there is not a sentence in line 
        // and next line is blank line, count as a sentence
        if (nosentence && line.isBlank()) {
            nosentence = false; 
            sentence_groups++;
        }
        return sentence_groups;
    }
```

* Refactoring: extract method
  - more readable code, less code duplication, and isolates independent parts of code, meaning that errors are less likely.

```java
    public int countSentence(String line) {
        int sentence_groups = 0;
        String[] lines = line.split("\\s+");
        final String punctuation = ".;!?";
        for (String word : lines) {
            // the word end with ".", ";", "!", "?"
            if (word.endsWith(punctuation)) {
                String sub_word = word.substring(0, word.length() - 1);
                if (isNumeric(sub_word)) sentence_groups--;
                sentence_groups++;
            }
        } // end of the line

        sentence_groups = checkSentence(line, sentence_groups);
        return sentence_groups;
    }

    private int checkSentence(String line, int sentence_groups) {
        if (sentence_groups == 0) nosentence = true;
        // if there is not a sentence in line
        // and next line is blank line, count as a sentence
        if (nosentence && line.isBlank()) {
            nosentence = false;
            sentence_groups++;
        }
        return sentence_groups;
    }
```

### level() Method

In the `src/readability/Calculation.java` class

https://github.com/b6210545602/PA4/blob/master/src/readability/Calculation.java

consider this code:

```java

    public String level(double index) {
        if (index > 100) return "4th grade student (elementary school)";
        else if (index > 90) return "5th grade student";
        else if (index > 80) return "6th grade student";
        else if (index > 70) return "7th grade student";
        else if (index > 65) return "8th grade student";
        else if (index > 60) return "9th grade student";
        else if (index > 50) return "High school student";
        else if (index > 30) return "College student";
        else if (index > 0) return "College graduate";
        else return "Advanced degree graduate";
    }
```
* Refactoring Signs: 
 - too many "if", "else" conditions.
 - many calls to index.

* Refactoring: Substitute Algorithm, Use of Enums.

```java
    public String level(double index) {
        return Level.get_index(index).printLevel();
    }
```

In the new `src/readability/Level.java` class

```java
    package readability;

    public enum Level {
        ADVANCE(-1, "Advanced degree graduate"),
        GRADUATE(0, "College graduate"),
        COLLEGE(30, "College student"),
        HIGH_SCHOOL(50, "High school student"),
        NINTH(60, "9th grade student"),
        EIGHTH(65, "8th grade student"),
        SEVENTH(70, "7th grade student"),
        SIXTH(80, "6th grade student"),
        FIFTH(90, "5th grade student"),
        FOURTH(100, "4th grade student (elementary school)");

        private final int index;
        private final String levelText;

        private Level(int index, String levelText) {
            this.levelText = levelText;
            this.index = index;
        }

        public static Level get_index(double value) {
            Level level = ADVANCE;
            for (Level l : values()) {
                if (value > l.index) {
                    level = l;
                }
            }
            return level;
        }
        public String printLevel() {
            return levelText;
        }

    }
```

