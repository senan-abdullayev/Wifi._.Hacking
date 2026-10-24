
Hashcat rules allow you to dynamically transform entries from a wordlist into countless password variations during runtime. This eliminates the need to store every permutation in a file, making password attacks more efficient and scalable.

![Wordlist transformation: "hackthebox" generates variations with rules: "Hackthebox," "h@ckth3box," "H@ckTh3box," "hackthebox1," "Hackthebox1!".](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/312/rules/rules.png)

## What Are Hashcat Rules?

Rules are sets of instructions applied to each `word` in a `wordlist`, generating variations through transformations like character substitutions, case changes, reversals, and symbol appending. By mimicking the kinds of modifications users commonly make to their passwords, these rules significantly expand our coverage and boost the chances of a successful crack.

For example, rules have the ability to:

- Append/prepend characters (e.g., `Password` → `Password1`)
- Replace characters (`a` → `@`)
- Capitalize letters (`password` → `PASSWORD`)
- Reverse words (`password` → `drowssap`)

---

#### Hashcat Example Rule Operations

|**Rule**|**Description**|**Example**|
|---|---|---|
|`c`|Capitalize the first character, lowercase the rest|password → Password|
|`C`|Lowercase the first character, uppercase the rest|password → pASSWORD|
|`t`|Toggle the case of all characters in word|password → PASSWORD|
|`T2`|Toggle the case of characters at position 3|password → paSsword|
|`$1`|Append 1 to the end|password → password1|
|`^1`|Prepend 1 to the front|password → 1password|
|`r`|Reverse the word|password → drowssap|
|`sa@`|Substitute a with @|password → p@ssword|
|`d`|Duplicate the word|password → passwordpassword|
|`z5`|Duplicate first character 5 times|password → ppppppassword|
|`Z5`|Duplicate last character 5 times|password → passwordddddd|

### Creating Hashcat Rule

Let's consider a scenario where we want to create a Hashcat rule to convert the word `winteriscoming` into `W1nt3r1sc0m1ng!`. This transformation involves several steps.

- Capitalizing the first letter (`w → W`)
- Replacing specific characters: (`i → 1, e → 3, o → 0`)
- Appending an exclamation mark (`!`) at the end

To achieve this, we can create the following Hashcat rule:

```
Snannn@htb[/htb]$ cat hashcatrule

T0si1se3so0$!
```

Here is a quick breakdown of the rule's syntax:

- `T0` — Capitalize the character at position 0 (the first letter)
- `si1` — Substitute all instances of i with 1
- `se3` — Substitute all instances of e with 3
- `so0` — Substitute all instances of o with 0
- `$!` — Append ! to the end of the word

This rule can be saved in a file, and when used with Hashcat during a password cracking session, the transformations will be applied to the wordlist in real-time.

We can test the Hashcat rule on a wordlist using the `-r` option along with `--stdout` to display the output.


```
Snannn@htb[/htb]$ cat wordlist

winteriscoming
```

```
Snannn@htb[/htb]$ hashcat -r hashcatrule wordlist --stdout

W1nt3r1sc0m1ng!
```

As demonstrated in the output above, the custom rule successfully transformed the word `winteriscoming` into `W1nt3r1sc0m1ng!` from the wordlist.

### Using Hashcat Rules

Hashcat has several built-in rule files available in the `rules/` directory:

```
ls -l /usr/share/hashcat/rules/
```

To apply a rule file to your wordlist in Hashcat, use the `-r` option followed by the path to the rule file. For example, to use `T0XlC.rule`, we would run the command like this:

```
hashcat -m 22000 wpa_hash wordlist.txt -r /usr/share/hashcat/rules/T0XlC.rule
```

There are a variety of publicly available, well-known rules and collections, such as:

|**Rule**|**Description**|**Link**|
|---|---|---|
|`one-rule-to-rule-them-all`|One super rule that tops all the tests|[Link](https://github.com/stealthsploit/OneRuleToRuleThemStill/blob/main/OneRuleToRuleThemStill.rule)|
|`rules-collection`|Largest collection of hashcat rule-files|[Link](https://github.com/n0kovo/hashcat-rules-collection)|
|`nsa-rules`|Rules generated from cracked passwords|[Link](https://github.com/NSAKEY/nsa-rules)|
|`Hob0Rules`|Rule based on statistics and industry patterns|[Link](https://github.com/praetorian-inc/Hob0Rules)|


