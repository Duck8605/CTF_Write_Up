# T-Jugle

## Description:

PHP is a loosely typed language, which can lead to some... interesting behaviors. Look at the code provided. Can you find an input that makes the comparison true? It's all about how you juggle the types.

## Initial Analysis

### Functionality Description
The website is very simple, displaying only a snippet of PHP source code. The goal is to provide a `password` value via a URL parameter to satisfy the condition in the code and retrieve the flag.

### PHP Source Code
```php
<?php
$flag_is_here = '0e251599870183182103338537534434';
$password = $_GET['password'];

if ($flag_is_here == md5($password)) {
    // Display the flag
}
?>
```

### Identifying Anomalies and Vulnerability Hypothesis
The anomaly, and the crux of the vulnerability, lies in the `if` statement. It uses the loose comparison operator `==` instead of the strict comparison operator `===`.
This is a **Type Juggling** vulnerability in PHP.

- When comparing a string to a number using the `==` operator, PHP will attempt to "type cast" the string into a number.
- Specifically, if a string starts with `0e` followed by digits (e.g., `0e12345`), PHP interprets it as scientific notation, i.e., `0 * 10^12345`, which always evaluates to `0`.

## Exploitation Steps

1.  **Analyze the `if` Condition:**
    - The variable `$flag_is_here` has the value `'0e251599870183182103338537534434'`. Because it starts with `0e...`, during a loose comparison, PHP will cast it to the number `0`.
    - Our goal is to find an input string (`$password`) such that `md5($password)` is also a string that starts with `0e` followed only by digits. Then, `md5($password)` will also be cast to the number `0`.
    - The condition will become `0 == 0`, which is `true`.

2.  **Find a "Magic Hash":**
    Such strings are known as "magic hashes." Several well-known strings, when hashed with MD5, produce a result starting with `0e...`. One of the most common and memorable is `240610708`.
    - `md5('240610708')` = `0e462097431906509019562988736854`
    This hash perfectly satisfies the condition.

3.  **Submit the Payload and Get the Flag:**
    We just need to pass the string `240610708` as the `password` parameter in the URL.
    **Final URL:** `[URL]?password=240610708`
    After visiting this URL, the comparison condition in the PHP code will be met, and the website will return the flag.

    ![image](https://github.com/user-attachments/assets/31c7106e-f4c2-4ee3-9ffc-34e1d3799a95)

    **Flag:** FLAG(852844f3594b5a7befcb90d3a4402d74)

## Impact
- **Severe:** This vulnerability allows an attacker to completely bypass authentication or hash-based checks without knowing the original value. Any function that uses loose comparison (`==`) to check hash values is vulnerable to this type of attack.

## Remediation
- **Always Use the Strict Comparison Operator (`===`):** This is the most definitive fix. The `===` operator checks both value and type. If the types are different (e.g., string and number), it will immediately return `false` without performing any type casting.
    ```php
    if ($flag_is_here === md5($password)) {
        // ...
    }
    ```
- **Use the `hash_equals()` function:** For comparing hash strings, PHP provides the `hash_equals()` function, which is specifically designed for this purpose. Not only does it perform a correct comparison, but it is also a timing-attack-safe comparison function.
