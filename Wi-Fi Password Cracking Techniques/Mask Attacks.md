Mask attacks (`-a 3`) are designed to generate passwords that follow a specific pattern or structure. They are particularly effective when we have partial knowledge about the password, such as its length, known characters, or format. Here are a few common scenarios:

- The password is exactly 8 characters long, and the last character is a number
- The password consists only of alphabetic characters (letters)
- The password begins with the company name, followed by a numeric sequence

In many situations, we may have no information about the target password. However, any available insights, such as known patterns, specific character types, or restrictions imposed by poorly designed systems or applications, can significantly improve the efficiency of a mask attack. Mask attacks are a more refined form of brute-force attacks and operate using Hashcat's `attack mode 3`, which allows for testing password combinations that follow a defined structure.

A mask can be created using static characters (e.g., fixed prefixes like "Admin" or "2024"), ranges of characters (e.g. `[a-z]` or `[A-Z0-9]`), or placeholders which represent specific sets of characters. Below is a list of commonly used Hashcat placeholders for mask attacks:

|**Mask**|**Description**|**Example**|
|---|---|---|
|`?l`|Lower-case ASCII letters|abcdefghijklmnopqrstuvwxyz|
|`?u`|Upper-case ASCII letters|ABCDEFGHIJKLMNOPQRSTUVWXYZ|
|`?d`|Digits|0123456789|
|`?h`|Digits with lower-case ASCII letters|0123456789abcdef|
|`?H`|Digits with upper-case ASCII letters|0123456789ABCDEF|
|`?s`|Special characters|«space»!"#$%&'()*+,-./:;<=>?@\^_{|
|`?a`|Combination of ?l, ?u, ?d and ?s|?l?u?d?s|
|`?b`|All possible byte values|?l?u?d?s|

In Hashcat, lowercase letters are denoted by `?l`, uppercase letters by `?u`, digits by `?d`, and special characters by `?s`. Together, these four sets make up the full range of 95 printable ASCII characters. To simplify mask creation, Hashcat provides a shortcut: `?a`, which includes all four sets `?l`, `?u`, `?d`, and `?s`. This allows us to define broader character ranges with a single symbol, making mask definitions more concise and efficient.

In Hashcat, lowercase letters are denoted by `?l`, uppercase letters by `?u`, digits by `?d`, and special characters by `?s`. Together, these four sets make up the full range of 95 printable ASCII characters. To simplify mask creation, Hashcat provides a shortcut: `?a`, which includes all four sets `?l`, `?u`, `?d`, and `?s`. This allows us to define broader character ranges with a single symbol, making mask definitions more concise and efficient.

---

## Fixed Length Brute Forcing

If we know the exact length of a password, we can use a mask to perform a fixed-length brute-force attack more easily.

For example, suppose we have the following information about the password:

- It is 14 characters long
- The first character is an uppercase letter
- The ninth character is a either digit or special character
- The last five characters are digits

Based on this information, we can construct a mask that reflects the known structure. An example of such a mask would be: `?u?l?l?l?l?l?l?l?a?d?d?d?d?d`.

Here is a breakdown of the mask:

- `?u` represents an uppercase letter for the first character
- `?l` represents lowercase letters for the next seven characters
- `?a` represents any alphanumeric character or symbol for the last six characters
- `?d` represents digits for the last five characters

We can perform a mask attack using the `-a 3` option in Hashcat, which specifies attack mode 3 (mask attack). By defining a fixed-length mask, we guide Hashcat to generate and test password candidates that follow a specific pattern.


```
hashcat -m 22000 -a 3 wpa_hash '?u?l?l?l?l?l?l?l?a?d?d?d?d?d'
```

- Possible match: `Password@54321`

We can also perform brute-forcing with a full mask such as `?a?a?a?a?a?a?a?a?a?a?a?a?a?a`. While it is technically possible, it is highly impractical since it could take hundreds or even thousands of years depending on available hardware resources and performance. Instead, optimizing our mask significantly reduces cracking time and increases the chances of success within a realistic timeframe.

## Variable Length Brute Forcing

When the length of the password is unknown, we can use variable-length brute-force attacks. Once we define a suitable mask pattern in Hashcat, one challenge is ensuring that the mask covers the correct length. If the mask is shorter than the actual password, the brute-force attempt will not succeed.

To address this, Hashcat offers an `incremental mode` that allows the mask length to increase gradually during the attack. This feature makes it possible to target a range of password lengths, rather than relying on a single fixed length. This dramatically increases the likelihood of recovering the correct password.

---

#### Mask Increments

Hashcat allows us to perform password guessing starting from the `minimum` supported length up to the `maximum` kernel length for each specific hash type. For example, when dealing with `mode 22000` (used for WPA2-EAPOL message pairs or PMKID hashes), the password must be between `8 and 63` characters. However, in real-world scenarios, users rarely choose passwords on the higher end of that range. Most commonly, passwords tend to fall between `8 and 16` characters.

This is where the `--increment` option becomes useful. It enables us to define both the minimum and maximum lengths for our password guesses. We can then specify a mask that matches the maximum length, and Hashcat will intelligently generate guesses starting from the minimum length and gradually increase up to the maximum.


```
hashcat -a 3 -m 22000 wpa_hash --increment --increment-min 8 --increment-max=14 '?u?l?l?l?l?l?l?l?d?d?d?d?d?d'
```

In the above example, we configure Hashcat to perform a brute-force attack with passwords ranging from 8 to 14 characters in length, using carefully selected character sets.

Let's quickly summarize each of the command-line flags:

- `-m 22000`: Specifies the WPA2-PMKID/EAPOL hash type.
- `-a 3`: Specifies a mask attack.
- `--increment`: Enables incremental guessing.
- `--increment-min=8` : Starts guessing from 8 characters.
- `--increment-max=14`: Stops at 14 characters.

By using these character classes, we aim to mimic common user password patterns such as starting with a capital letter, followed by lowercase characters, and ending with numbers or special symbols. Here's how Hashcat interprets the mask over increasing lengths:

- `?u?l?l?l?l?l?l?l` → Possible match: `Password`
- `?u?l?l?l?l?l?l?l?d` → Possible match: `Password1`
- `?u?l?l?l?l?l?l?l?d?d` → Possible match: `Password12`
- `?u?l?l?l?l?l?l?l?d?d?d` → Possible match: `Password123`
- `?u?l?l?l?l?l?l?l?d?d?d?d` → Possible match: `Password1234`
- `?u?l?l?l?l?l?l?l?d?d?d?d?d` → Possible match: `Password12345`
- `?u?l?l?l?l?l?l?l?d?d?d?d?d?d` → Possible match: `Password123456`

If a password is relatively short, there is a higher chance of recovering it quickly using a mask attack. However, as the length of the password increases, the number of possible combinations grows exponentially, making the attack significantly more time-consuming. Therefore, it is important to refine the mask as much as possible based on any known patterns. Whenever possible, it is recommended to try dictionary and rule-based attacks first, as they are generally more efficient and can yield results faster.

---

## Custom Character Sets

Mask increments allow us to perform variable-length brute-force attacks. But what if we know that the last five characters of the password are either digits or special characters?

One option is to use the built-in `?a` mask, which covers all four standard character types: lowercase (`?l`), uppercase (`?u`), digits (`?d`), and special characters (`?s`). However, an even better alternative is to define custom character sets tailored to what we know (or suspect) about the password structure.

For example, if we suspect the last character might be either a digit or a special character, we can define a custom set like this: `-1 ?d?s`.

Then, by using `?1` in the appropriate position within our mask, Hashcat will limit that position to only the characters defined in the custom set.

```
hashcat -a 3 -m 22000 wpa_hash --increment --increment-min 8 --increment-max=14 -1 ?d?s '?u?u?u?u?u?u?u?u?1?1?1?1?1'
```

This approach allows for more targeted brute-force attempts and helps reduce the overall keyspace.

---

#### Creating and Using .hcchr Files

Instead of hardcoding custom character sets into the command line, we can save them into `.hcchr` files for reuse and readability.

Suppose we define two custom character sets:

- Set `?1`: Uppercase and lowercase letters, plus digits  
    (`abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789`)
- Set `?2`: Lowercase letters, special characters, and digits  
    (`abcdefghijklmnopqrstuvwxyz!@#$%&*-+0123456789`)

We can save these sets into two different `.hcchr` files to use with Hashcat.

Now, to run a brute-force attack where the first six characters come from set `?1` and the last six from set `?2`, with the total password length ranging from 8 to 12 characters, our command would be:

```
hashcat -a 3 -m 22000 wpa_hash --increment --increment-min=8 --increment-max=12 -1 starterchars.hcchr -2 endchars.hcchr ?1?1?1?1?1?1?2?2?2?2?2?2
```

Breaking down the components:

- `-1 starterchars.hcchr`: Custom set for ?1
- `-2 endchars.hcchr`: Custom set for ?2
- `?1?1?1?1?1?1?2?2?2?2?2?2`: Mask using both sets

---

#### Using Multiple Custom Charsets

If we want to define and use multiple custom character sets in a Hashcat mask attack, we can use the `-1`, `-2`, `-3`, and `-4` switches together to assign different sets.

- -1 abcdefghijklmn?d
- -2 ?H?s
- -3 ?dabcdef
- -4 charsets/special/Russian/ru_ISO-8859-5-special.hcchr

```
hashcat -a 3 -m 22000 wpa_hash -1 abcdefghijklmn?d -2 ?H?s -3 ?dabcdefghi -4 charsets/special/Russian/ru_ISO-8859-5-special.hcchr ?1?2?3?4?1?2?3?4
```

