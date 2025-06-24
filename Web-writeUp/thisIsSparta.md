# Challenge 1: This is Sparta

## Initial Analysis

### Functionality Description
The web page presents a simple login interface with `username` and `password` input fields and a check button.

### Identifying Anomalies
Upon inspecting the source code, an obfuscated JavaScript snippet is found within the `<script>` tag, encoded in hexadecimal format.

```javascript
var _0xae5b=["\x76\x61\x6C\x75\x65","\x75\x73\x65\x72","\x67\x65\x74\x45\x6C\x65\x6D\x65\x6E\x74\x42\x79\x49\x64","\x70\x61\x73\x73","\x43\x79\x62\x65\x72\x2d\x54\x61\x6c\x65\x6e\x74","\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x43\x6F\x6E\x67\x72\x61\x74\x7A\x20\x0A\x0A","\x77\x72\x6F\x6E\x67\x20\x50\x61\x73\x73\x77\x6F\x72\x64"];
function check() {
    var _0xeb80x2 = document[_0xae5b[2]](_0xae5b[1]])[_0xae5b[0]];
    var _0xeb80x3 = document[_0xae5b[2]](_0xae5b[3]])[_0xae5b[0]];
    if (_0xeb80x2 == _0xae5b[4] && _0xeb80x3 == _0xae5b[4]) {
        alert(_0xae5b[5]);
    } else {
        alert(_0xae5b[6]);
    }
}
```

### Vulnerability Hypothesis
The login credentials are hardcoded directly in the client-side JavaScript. Decoding the script will reveal this information.

## Exploitation Steps

1.  **View Source Code:** Open the challenge page and use DevTools (F12) to inspect the source code, identifying the obfuscated JavaScript.

2.  **Decode the Script:** Use a Hex decoder tool (e.g., CyberChef, kt.gy) to decode the `_0xae5b` array. The decoded result is:
    ```javascript
    var _0xae5b = ["value", "user", "getElementById", "pass", "Cyber-Talent", " Congratz", "wrong Password"];
    ```

3.  **Analyze the Logic:** Reading the decoded JavaScript reveals the validation logic:
    *   It retrieves the value from the input field with `id="user"`.
    *   It retrieves the value from the input field with `id="pass"`.
    *   It compares both values to the string `_0xae5b[4]`, which is `"Cyber-Talent"`.
    *   If both match, a success message is displayed.

4.  **Retrieve the Flag:** Enter `Cyber-Talent` into both the `username` and `password` fields and click the "Check" button. An alert dialog will appear containing the flag.

    

## Impact
- **Severe:** The hardcoded credentials vulnerability allows any user with access to the source code to retrieve sensitive information (the flag in this case), completely bypassing the login mechanism.

## Remediation
- **Never Hardcode Credentials:** Never embed sensitive information like passwords or API keys in client-side code (HTML, JS, CSS).
- **Server-Side Validation:** Always perform authentication and validation checks on the server-side, never on the client-side.
- **Use HTTPS:** Encrypt the communication channel to protect data in transit.