# Log4Shell-Rex

The following RegEx was written in an attempt to match indicators of a Log4Shell (CVE-2021-44228 and
CVE-2021-45046) exploitation.

**If you run a version from pre 2021/12/20, it's highly recommended to test and update.**\
I've removed some quirks and enhanced performance.

The Regex aims being PCRE compatible, but should also run on re2 and potentially more RegEx engines.

**RegEx:**
```regex
(?im)(?:^|[\n])(?:[\x24]|%(?:25)*24|\\u?0*(?:44))(?:[\x7b]|%(?:25)*7b|\\u?0*(?:173))[^\n]*?((?:j|%(?:25)*(?:4a|6a)|\\u?0*(?:112|152))[^\n]*?(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))[^\n]*?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))[^\n]*?((?:l|%(?:25)*(?:4c|6c)|\\u?0*(?:154|114))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))(?:[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163)))?|(?:r|%(?:25)*(?:52|72)|\\u?0*(?:122|162))[^\n]*?(?:m|%(?:25)*(?:4d|6d)|\\u?0*(?:155|115))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))|(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))){2}[^\n]*?(?:o|%(?:25)*(?:4f|6f)|\\u?0*(?:157|117))[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))|(?:c|%(?:25)*(?:43|63)|\\u?0*(?:143|103))[^\n]*?(?:o|%(?:25)*(?:4f|6f)|\\u?0*(?:157|117))[^\n]*?(?:r|%(?:25)*(?:52|72)|\\u?0*(?:122|162))[^\n]*?(?:b|%(?:25)*(?:42|62)|\\u?0*(?:102|142))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))|(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:h|%(?:25)*(?:48|68)|\\u?0*(?:110|150))(?:[^\n]*?(?:t|%(?:25)*(?:54|74)|\\u?0*(?:124|164))){2}[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))(?:[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163)))?)[^\n]*?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))|(?:b|%(?:25)*(?:42|62)|\\u?0*(?:102|142))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))[^\n]*?(?:e|%(?:25)*(?:45|65)|\\u?0*(?:145|105)).{2,60}?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))(JH[s-v]|[\x2b\x2f-9A-Za-z][CSiy]R7|[\x2b\x2f-9A-Za-z]{2}[048AEIMQUYcgkosw]ke[\x2b\x2f-9w-z]))
```

## Capabilities

By now, this regex should match the exploit, regardless:

- Just logged
- Case insensitive (also in all supported encodings)
- URL Encoded
- Recursively URL Encoded
- With Unicode encoding
- With Octal encoding
- Base64 encoded (rudimentary)

### Background

The goal is to have a RegEx that represents a reasonable tradeoff between detecting as many attack
attempts as possible with an acceptable number of false positives.

The APT attacker will find a way around if necessary, but less elaborate attacks will leave the
warning light on.

Why a (single) RegEx: Because it can be easily executed on the CLI or in a SIEM without any
additional tools. If tools can be executed, do it, they exist.

The length of the regex is less of a problem than its performance. Despite the length, the RegEx
should be acceptably fast to execute on average log data.

### Call for action

I wanna make it hard to hide an attack in real world szenarios.

If this RegEx does not match sth. you have seen in the wild or can show being exploitable, please
create an issue.

It is known, that you can work around the RegEx easily by encoding different parts of the attack
pattern using Base64. How ever, this is accepted, as `base64` did not finaly made it into an
official Log4j release yet. ([LOG4J2-2446](https://issues.apache.org/jira/projects/LOG4J2/issues/LOG4J2-2446))

### Screenshot

![Example Screenshot](screenshots/example_2.png)

Test on:
- **[regex101](https://regex101.com/r/KqGG3W/3)**
- **[CyberChef](https://gchq.github.io/CyberChef/#recipe=Regular_expression('User%20defined','put%20the%20regex%20here',true,true,false,false,false,false,'Highlight%20matches')&input=UHV0IFlvdXIgSW5wdXQgSGVyZQ)**


## Hunting on your Linux machine

### On the CLI with `grep`

```bash
eval "$(./RegEx_Generator.sh)"
grep -P ${Log4ShellRex} <logfile>
```

```bash
grep -P '(?im)(?:^|[\n])(?:[\x24]|%(?:25)*24|\\u?0*(?:44))(?:[\x7b]|%(?:25)*7b|\\u?0*(?:173))[^\n]*?((?:j|%(?:25)*(?:4a|6a)|\\u?0*(?:112|152))[^\n]*?(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))[^\n]*?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))[^\n]*?((?:l|%(?:25)*(?:4c|6c)|\\u?0*(?:154|114))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))(?:[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163)))?|(?:r|%(?:25)*(?:52|72)|\\u?0*(?:122|162))[^\n]*?(?:m|%(?:25)*(?:4d|6d)|\\u?0*(?:155|115))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))|(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))){2}[^\n]*?(?:o|%(?:25)*(?:4f|6f)|\\u?0*(?:157|117))[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))|(?:c|%(?:25)*(?:43|63)|\\u?0*(?:143|103))[^\n]*?(?:o|%(?:25)*(?:4f|6f)|\\u?0*(?:157|117))[^\n]*?(?:r|%(?:25)*(?:52|72)|\\u?0*(?:122|162))[^\n]*?(?:b|%(?:25)*(?:42|62)|\\u?0*(?:102|142))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))|(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:h|%(?:25)*(?:48|68)|\\u?0*(?:110|150))(?:[^\n]*?(?:t|%(?:25)*(?:54|74)|\\u?0*(?:124|164))){2}[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))(?:[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163)))?)[^\n]*?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))|(?:b|%(?:25)*(?:42|62)|\\u?0*(?:102|142))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))[^\n]*?(?:e|%(?:25)*(?:45|65)|\\u?0*(?:145|105)).{2,60}?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))(JH[s-v]|[\x2b\x2f-9A-Za-z][CSiy]R7|[\x2b\x2f-9A-Za-z]{2}[048AEIMQUYcgkosw]ke[\x2b\x2f-9w-z]))' <logfile>
```

### Combine it with `find` to recursively scan a (sub-)folder of log files

```bash
eval "$(./RegEx_Generator.sh)"
find /var/log -name "*.log" | xargs grep -P ${Log4ShellRex}
```

```bash
find /var/log -name "*.log" | xargs grep -P '(?im)(?:^|[\n])(?:[\x24]|%(?:25)*24|\\u?0*(?:44))(?:[\x7b]|%(?:25)*7b|\\u?0*(?:173))[^\n]*?((?:j|%(?:25)*(?:4a|6a)|\\u?0*(?:112|152))[^\n]*?(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))[^\n]*?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))[^\n]*?((?:l|%(?:25)*(?:4c|6c)|\\u?0*(?:154|114))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))(?:[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163)))?|(?:r|%(?:25)*(?:52|72)|\\u?0*(?:122|162))[^\n]*?(?:m|%(?:25)*(?:4d|6d)|\\u?0*(?:155|115))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))|(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:[^\n]*?(?:[i\x{130}\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\u?0*(?:111|151|460|461))){2}[^\n]*?(?:o|%(?:25)*(?:4f|6f)|\\u?0*(?:157|117))[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))|(?:c|%(?:25)*(?:43|63)|\\u?0*(?:143|103))[^\n]*?(?:o|%(?:25)*(?:4f|6f)|\\u?0*(?:157|117))[^\n]*?(?:r|%(?:25)*(?:52|72)|\\u?0*(?:122|162))[^\n]*?(?:b|%(?:25)*(?:42|62)|\\u?0*(?:102|142))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))|(?:n|%(?:25)*(?:4e|6e)|\\u?0*(?:156|116))[^\n]*?(?:d|%(?:25)*(?:44|64)|\\u?0*(?:144|104))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))|(?:h|%(?:25)*(?:48|68)|\\u?0*(?:110|150))(?:[^\n]*?(?:t|%(?:25)*(?:54|74)|\\u?0*(?:124|164))){2}[^\n]*?(?:p|%(?:25)*(?:50|70)|\\u?0*(?:160|120))(?:[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163)))?)[^\n]*?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))|(?:b|%(?:25)*(?:42|62)|\\u?0*(?:102|142))[^\n]*?(?:a|%(?:25)*(?:41|61)|\\u?0*(?:101|141))[^\n]*?(?:[s\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\u?0*(?:123|577|163))[^\n]*?(?:e|%(?:25)*(?:45|65)|\\u?0*(?:145|105)).{2,60}?(?:[\x3a]|%(?:25)*3a|\\u?0*(?:72))(JH[s-v]|[\x2b\x2f-9A-Za-z][CSiy]R7|[\x2b\x2f-9A-Za-z]{2}[048AEIMQUYcgkosw]ke[\x2b\x2f-9w-z]))'
```

## Hunting in your logs using Splunk

You can use this RegEx to search your indexed logs using the `| regex`
[SPL](https://docs.splunk.com/Documentation/Splunk/latest/SearchReference/Regex) command

```spl
index=<...> sourcetype=<...>
| regex "<Log4ShellRex>"
```

```spl
index=<...> sourcetype=<...>
| regex "(?im)(?:^|[\\n])(?:[\\x24]|%(?:25)*24|\\\\u?0*(?:44))(?:[\\x7b]|%(?:25)*7b|\\\\u?0*(?:173))[^\\n]*?((?:j|%(?:25)*(?:4a|6a)|\\\\u?0*(?:112|152))[^\\n]*?(?:n|%(?:25)*(?:4e|6e)|\\\\u?0*(?:156|116))[^\\n]*?(?:d|%(?:25)*(?:44|64)|\\\\u?0*(?:144|104))[^\\n]*?(?:[i\\x{130}\\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\\\u?0*(?:111|151|460|461))[^\\n]*?(?:[\\x3a]|%(?:25)*3a|\\\\u?0*(?:72))[^\\n]*?((?:l|%(?:25)*(?:4c|6c)|\\\\u?0*(?:154|114))[^\\n]*?(?:d|%(?:25)*(?:44|64)|\\\\u?0*(?:144|104))[^\\n]*?(?:a|%(?:25)*(?:41|61)|\\\\u?0*(?:101|141))[^\\n]*?(?:p|%(?:25)*(?:50|70)|\\\\u?0*(?:160|120))(?:[^\\n]*?(?:[s\\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\\\u?0*(?:123|577|163)))?|(?:r|%(?:25)*(?:52|72)|\\\\u?0*(?:122|162))[^\\n]*?(?:m|%(?:25)*(?:4d|6d)|\\\\u?0*(?:155|115))[^\\n]*?(?:[i\\x{130}\\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\\\u?0*(?:111|151|460|461))|(?:d|%(?:25)*(?:44|64)|\\\\u?0*(?:144|104))[^\\n]*?(?:n|%(?:25)*(?:4e|6e)|\\\\u?0*(?:156|116))[^\\n]*?(?:[s\\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\\\u?0*(?:123|577|163))|(?:n|%(?:25)*(?:4e|6e)|\\\\u?0*(?:156|116))[^\\n]*?(?:[i\\x{130}\\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\\\u?0*(?:111|151|460|461))[^\\n]*?(?:[s\\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\\\u?0*(?:123|577|163))|(?:[^\\n]*?(?:[i\\x{130}\\x{131}]|%(?:25)*(?:49|69|C4%(?:25)*B0|C4%(?:25)*B1)|\\\\u?0*(?:111|151|460|461))){2}[^\\n]*?(?:o|%(?:25)*(?:4f|6f)|\\\\u?0*(?:157|117))[^\\n]*?(?:p|%(?:25)*(?:50|70)|\\\\u?0*(?:160|120))|(?:c|%(?:25)*(?:43|63)|\\\\u?0*(?:143|103))[^\\n]*?(?:o|%(?:25)*(?:4f|6f)|\\\\u?0*(?:157|117))[^\\n]*?(?:r|%(?:25)*(?:52|72)|\\\\u?0*(?:122|162))[^\\n]*?(?:b|%(?:25)*(?:42|62)|\\\\u?0*(?:102|142))[^\\n]*?(?:a|%(?:25)*(?:41|61)|\\\\u?0*(?:101|141))|(?:n|%(?:25)*(?:4e|6e)|\\\\u?0*(?:156|116))[^\\n]*?(?:d|%(?:25)*(?:44|64)|\\\\u?0*(?:144|104))[^\\n]*?(?:[s\\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\\\u?0*(?:123|577|163))|(?:h|%(?:25)*(?:48|68)|\\\\u?0*(?:110|150))(?:[^\\n]*?(?:t|%(?:25)*(?:54|74)|\\\\u?0*(?:124|164))){2}[^\\n]*?(?:p|%(?:25)*(?:50|70)|\\\\u?0*(?:160|120))(?:[^\\n]*?(?:[s\\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\\\u?0*(?:123|577|163)))?)[^\\n]*?(?:[\\x3a]|%(?:25)*3a|\\\\u?0*(?:72))|(?:b|%(?:25)*(?:42|62)|\\\\u?0*(?:102|142))[^\\n]*?(?:a|%(?:25)*(?:41|61)|\\\\u?0*(?:101|141))[^\\n]*?(?:[s\\x{17f}]|%(?:25)*(?:53|73|C5%(?:25)*BF)|\\\\u?0*(?:123|577|163))[^\\n]*?(?:e|%(?:25)*(?:45|65)|\\\\u?0*(?:145|105)).{2,60}?(?:[\\x3a]|%(?:25)*3a|\\\\u?0*(?:72))(JH[s-v]|[\\x2b\\x2f-9A-Za-z][CSiy]R7|[\\x2b\\x2f-9A-Za-z]{2}[048AEIMQUYcgkosw]ke[\\x2b\\x2f-9w-z]))"
```

## Other

**Please create a pull request / issue if you can provide syntax for more systems.**

## Credits

I got help and ideas from:

- [@cyberops](https://twitter.com/cyb3rops) building [log4shell-detector](https://github.com/Neo23x0/log4shell-detector/) that served as an inspiration
- [@karanlyons](https://github.com/karanlyons) providing corpus to test against
