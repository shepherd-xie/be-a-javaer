# Java 13

- Dynamic CDS Archives
- ZGC: Uncommit Unused Memory
- Reimplement the Legacy Socket API
- Switch Expressions (Preview)
- Text Blocks (Preview)

## Text Blocks (Preview)

```java
String query = """
    SELECT id, name FROM employee
    WHERE CITY = 'California'
    ORDER BY dept_no;
""";
```

